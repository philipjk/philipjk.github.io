---
layout: post
title: "The attention mechanism, Magic: The Gathering, and in between"
date:   2025-02-27 10:18:14 +0100
categories: app development
---

There is almost always value in reasoning from first princples. Why do LLMs work
better than anything we had before? Sure, LSTMs and the likes were quite an
over-engineered model, and that rarely reflects something that we can scale - 
in terms of size and entity of the tasks.

For some time I thought the sole advantage of the transformer architecture
and the attention mechanism was that, compared to the old recurrent networks (RNNs),
was in fact, not recurrent. Or better, considering that the attention mechanism 
could process the whole input/output sequence in one shot, the advatage lied
in the large scale parallelization compared to RNNs, which instead required
processing sequentially over the time dimension of the input/output sequence.

The only (ok, well, not the only one but let's focus here) limiting factor in scaling
the input sequence length to an LLM is its memory footprint, whereas recurrent
networks incur in many theoretical and practical issues with the size of the
sequence.

On the downside, the attention mechanism completely disregards the ordering of the
inputs in a sequence, which instead comes for free in RNNs. This limiting factor was 
circumvented with the introduction of positional encoding (done in many different
ways) to basically embed the relative position of the inputs in the input 
features themselves. To my surprise, I realized I am not aware of applications of the
attention mechanism where this fact is instead leveraged to an advantage, for
instance for problems where the size of the input changes and the ordering is not
important, e.g. for set/multiset inputs. Usually, an unordered input is problematic 
for other neural network architectures, where each neuron expects an input
representing something specific. Notably, CNNs of a decade ago had a hard time
recognizing upside-down pictures.

But really, the advantage of the attention mechanism, as I came later to realize,
comes from introducing an operation that was overlooked in other models, i.e.
the product among inputs. In all likelyhood, to someone outside of the loop, this 
seems absolutely trivial. And in all honesty, one could wonder why limit ourselves 
to the product and not try something more exotic like an exponentiation?

Anyway, the product between input variables resulted in a major advancement, and
it is possibly the biggest improvement we had in a long time in neural networks.
Essentially, when the input sequence is a sentence, it helps determine
how much each word _attends_ to any other. Attending here has a singular meaning,
that is: given the context of words in a sentene, which other words does this 
specific word care about the most?

## A draft game of Magic
Magic: The Gathering (MTG) is largely acknowledged (by myself, unanimously) as the king
of trading card games. At its core, it provides a list of rules and multiple sets
of cards you can play with in different formats.

A draft game in MTG is a format where players build decks on the spot
by selecting cards from rotating packs. Each player starts with a booster pack, 
picks one card, and then passes the remaining cards to the next player. This 
process continues until all cards are chosen, then a new pack is opened, and the
drafting cycle repeats. Once all packs have been drafted, players build decks 
from their selected cards and compete against each other. This format tests a 
player's ability to evaluate cards dynamically and adapt to evolving strategies 
based on available choices.

At any  moment of the drafting phase, a player's state is fully defined by:
- the pool of cards they have already drafted
- the pack of cards they have just received

With the objective of building the best deck to win against the other players,
the choice of the best card largely depends on multiple factors, and there almost
never is a _best card_ in the vacuum, outside of its context.

If we wanted (and _we wanted_) to build a draft assistant to suggest the ideal
card to a player, given their state, we would need to model a probability distribution
over the cards in the pack to figure out which one to suggest. We would need to consider
- inputs that are an unordered set of (pool) cards
- given the context of cards in the pool, which card in the pack we care about the most

### Draft assistants
MTG can be played on paper (i.e. with real cards) or online with the Arena 
client and digital assets. Arena gives access to the game log, where events
are recorded along with the game states. Companies and crowdsourced efforts
have built all kinds of assistants from the Arena logs. Draft games in Arena 
include 8 players, and (in _Premier_ Drafts) you play unutil you reach 7 wins or
3 losses, whichever comes first. Typically, you are assigned a player rank 
based on your past stats, and you will be matched with players of roughly the
same rank, kind of like what happens with chess.

[17lands](https://www.17lands.com/) is a community that aggregates and shares 
structured data from game logs, and provides statistical insights on the current
metagame, specifically for _limited_ formats like drafts. Fan-made tools provide
user interfaces for this data to Arena players, for free, giving access to
statistical results for each card, e.g. the win rate when card $x$ was in the 
opening hand.

Among the commercial solutions, [Untapped.gg](https://www.untapped.gg/) is one example
of a company providing, under a paid subcription, a more nuanced draft assistant
(_Draftsmith_) for picking the best card at each time of the drafting phase.
Although the implementation details are not disclosed, it provides a score for
each card in the pack based on the current pool and metagame.

## A data-based draft assistant
Having access to the Arena log is amazing, until you open one and try to make sense
of the million of lines it prints for a couple games worth of MTG. Finding the 
easiest and leanest way to acces all and only the lines I needed for building a 
draft assistant took quite some time, but I'll spare the details here.

With the boring part out of the way (and one yet to come in building the UI), I
could focus on modeling the problem, and finding a solution.  We want our model to
estimates the probability distribution:

$$
P(x | y, z, w, k)
$$

where:
- $x \in y$ is the selected card,
- $y$ is the pack of available cards,
- $z$ is the player's current card pool,
- $w$ represents the expected number of wins,
- $k$ denotes player rank.

Instead of using external heuristics like Game-in-Hand Win Rate, our model 
learns pick probabilities directly from hundreds of thousands of draft data points
from 17lands. At inference time, we 
condition the model on an optimal scenario by setting $w = 7$ and $k =$ _Mythic 
rank_, ensuring it recommends the best choices for winning a draft.

## The model

In a Magic: The Gathering draft, a player selects cards from a pack to build
their pool. The key challenge is evaluating each card in the pack based on its 
synergy with the pool. This is an ideal problem for cross-attention, where:

- The query comes from the pack, representing the cards under consideration.
- The keys and values come from the pool, representing the context for making decisions.

Unlike self-attention (which relates elements within the same set), 
cross-attention establishes relationships between two different sets: the 
current pack and the player's pool.

The DraftPicker model follows a modified cross-attention mechanism.

#### 1. Embedding the Inputs
Each card in the pool and pack is embedded into a fixed-dimensional space:

$$
\text{pool}_{emb} \in \mathbb{R}^{B \times n_{\text{pool}} \times d}
$$

$$
\text{pack}_{emb} \in \mathbb{R}^{B \times n_{\text{pack}} \times d}
$$

where:
- $ B $ is the batch size,
- $ n_{pool} $ is the number of cards in the player's pool,
- $ n_{pack} $ is the number of cards in the pack,
- $ d $ is the embedding dimension.

Additional embeddings for expected wins and player rank are also added, then 
concatenated with the pool embeddings.

#### 2. Computing Query, Key, and Value Projections
The model projects the embeddings into different spaces:

$$
Q = W_Q \cdot \text{pack}_{emb} \quad \in \mathbb{R}^{B \times n_{pack} \times d'}
$$

$$
K = W_K \cdot \text{pool}_{emb} \quad \in \mathbb{R}^{B \times n_{pool} \times d'}
$$

$$
V = W_V \cdot \text{pool}_{emb} \quad \in \mathbb{R}^{B \times n_{pool} \times d'}
$$

where:
- $ W_Q, W_K, W_V $ are learned projection matrices,
- $ d' $ is the internal attention dimension (set to 8 in this model),
- $ Q $ represents queries (from the pack),
- $ K $ and $ V $ represent keys and values (from the pool).

#### 3. Computing Attention Scores
The raw attention scores are computed via dot product similarity:

$$
S = \frac{Q K^T}{\sqrt{d'}}
$$

where:
- $ S \in \mathbb{R}^{B \times n_{\text{pack}} \times n_{\text{pool}}} $,
- Each entry $ S_{ij} $ represents how well card $ i $ in the pack matches card $ j $ in the pool.

The division by $ \sqrt{d'} $ stabilizes gradients and prevents large values.

#### 4. Applying Softmax (Across the Pool)
To convert scores into attention weights, we apply softmax along the last dimension:

$$
A = \text{softmax}(S, \text{dim}=-1)
$$

This ensures that:
- Each row $ A_{i,:} $ sums to 1,
- The model assigns higher weight to pool cards that are more relevant to each pack card.

#### 5. Computing Context Vectors
We compute the weighted sum of value vectors:

$$
C = A V
$$

where:
- $ C \in \mathbb{R}^{B \times n_{\text{pack}} \times d'} $,
- Each card in the pack now has a contextualized representation based on how it relates to the player's pool.

#### 6. Final Scoring
A final scorer layer computes logits:

$$
\text{logits} = W_{\text{out}} \cdot \text{LeakyReLU}(C)
$$

where:
- The output has shape $ \mathbb{R}^{B \times n_{\text{pack}}} $,
- This produces a score for each pack card, indicating how well it fits the pool.

## The result

In this [Gihub repo](https://github.com/philipjk/draft_assistant) you can find
the "front-end" of the assistant, a python-based terminal script to suggest 
the best cards in a Premier Draft game of MTG on Arena. So far, only _MTG: Foundations_
is supported, but expanding to other card sets if just a matter of productionizing
model training. Admittedly, being run from terminal is a limiting factor to its
adoption but building a UI is on the roadmap. 