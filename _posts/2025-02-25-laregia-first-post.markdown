---
layout: post
title: "LaRegia - an app for all things LaZanzara"
subtitle:  "Approaching Android development in 2025 with a meme app?"
date:   2025-02-25 10:18:14 +0100
categories: app development
---

I have tried a couple times to approach app development, with little to no
avail. Beyond learning a programming language (Java/Kotlin), which is the easy
part, there's a plethora of API's and syntaxes and constructs you need to 
memorize that felt discouraging every single time. Admittedly, my oh my, 
when I decided to deep dive into a specific platform, it was Windows 
Mobile with `C#`. I assume I do not need to comment any further on the topic.

On the bright side of things, I am now comfortable reading Kotlin code, and for 
about $20/month you have access to an insatiable junior developer in ChatGPT.
So, with the boring part of writing boilerplate out of the picture, I decided to 
start working on a couple projects, almost exclusively for fun. With that I mean
I see the value in learning Android development, and my reasonably theoretical 
PhD studies started my cravings for something more tangible and applicable, but
the choice of what to work on is purely based on the result (and to some degree the
development) being a lot of fun. That is at least for the niche I am targeting. 

## Why?

I reply to a lot of texts with GIFs, which makes communication possible on
an alternative channel, that of the inner joke. From a user's perspective,
GIFs are easy: the sender thinks of a pop culture reference, opens basically any
social media app search box and looks for it. The sender decides which one to 
share with a glance. The receiver does not even need to read a text, and a quick
look is enough. The speed of communication depends on the medium (image vs text)
only to a certain degree, and I believe the time saved comes from information 
compression. This compression in turns comes from the pop culture 
reference expressing a whole set of ideas and sentiments that would take a lot 
of words to express otherwise.
<div style="text-align: center">
    <img src="/assets/images/posts/no-god-no-god-please-no.gif" alt="no-god-no">
</div>

Audio messages are basically the opposite. The receiver
can't just glance at their phone and see what's going on, they need to listen to
the whole thing. But audio messages add a lot of flavor to the conversation,
they're more intimate and spare time to the sender. 

The initial idea of LaRegia was to merge the two worlds, that is to take from:
- GIFs: the inner-joke channel of communication, and the information compression
- Audio messages: flavor and speed. Also, I'll shamelessly admit that I wanted to try this
because I am not aware of any other app for sharing audio-memes.

But to increase the odds of my bet, that is that anyone would use the app, I wanted
to target a niche community where the inner-joke channel would be extremely solid,
meaning:
- the inner jokes *need* to be understood from both ends of the channel
- the channel needs to be exclusive, a fast and only track from A to B


## What?

### Audio-meme sharing

La Zanzara is the most listened-to podcast in Italy, without even being a podcast
first. It is broadcast every weekday on the radio, and episodes are then uploaded
to all audio streaming platforms. Recently, it was recognized as the first 
Italian podcast to reach 50mil streams on Spotify.

It is a satyrical (and very politically polarizing) show where the two hosts
~~shout at each other~~ _discuss_ news events. Thrown in the mix are a number of
despicable guests that support either sides of an argument, and are essentially 
made fun of by the hosts. Paolo Mieli, esteemed journalist and fan of the show, 
famously and ironically lists the average guest (which I refuse to translate):

> "Necrofili, coppie scambiste, neonazisti, vetero nazisti, erotomani, fascisti e 
> comunisti reazionari, filo-Putiniani e antisemiti, satanisti e no-vax infoiati, 
> pornografi e baciabili, camionisti truculenti, gente che parla con gli ufo, con 
> le viscere degli abbacchi e con lo spirito santo"


Beside the two hosts, from my point of view, a great deal of fun comes from the
interaction of the show director with the two hosts, exclusively mediated by
the insertion of 1-2 second audio clips in the live show. These audio clips are
references to old absurd claims from old guests, outrageous comments made by 
political figures, sounds and noises that almost by definition are audio-memes.

The MVP of LaRegia (i.e. The Show Director) let's you share these audio-memes on
all messaging apps.

<div style="display: flex; justify-content: space-between; max-width: 100%;">
    <img src="/assets/images/posts/zanza-1.jpg" alt="Image 1" style="width: 49%;">
    <img src="/assets/images/posts/zanza-2.jpg" alt="Image 2" style="width: 49%;">
</div>

### Everything else

As I was in the development stage of the app, and I wanted to gather some feedback,
I noticed how the internal/alpha testers and in general the fans of the show were
all very interested in the songs the show directors would play as background music,
or to introduce a new character. That is something I was also looking for myself,
and fan-made playlists on Spotify would only go so far, especially in terms of almost
never being up to date.

So I decided that would make for an interesting addition, and I am actually 
considering making it the main feature and page of the app.

<div style="text-align: center">
    <img src="/assets/images/posts/zanza-3.jpg" alt="Image 3" style="width: 50%;">
</div>

That was pretty cool to develop, and I'm quite happy with the result so far.
Right now, beside the front-end (which does need some improvement), the interesting
part works under the hood, where a Google Cloud Run Job instance is launched daily
aftet the show is published to do a couple things:
- fetch the latest episode
- process it to find audio segments (a painful throwback to Tensorflow, which I 
had hoped not to encounter again after converting to Pytorch)
- sort of _shazam_ the audio segment to find title and artist

The front-end just needs to put everything together and find the 
right album art and provide a short preview of the song (beside saving favorites 
and all of that). Since it was basically a no-brainer, it also lets you play 
the full episode directly fom the app!

## Now?

I'm still in the closed testing phase of the app development cycle. Unfortunately,
just a few months before starting my development account on the Play Store, they
introduced much stricter rules and requirements to go for open testing and
giving production access.

On the plus side, directly interacting with the testers at this stage
is an interesting chance to build a small community of avid
users who feel involved in the development phase, and rightfully so.

If you wish to be included as a tester, send me an email at `watersheepapps@gmail.com`.
