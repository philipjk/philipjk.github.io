source "https://rubygems.org"

gem "jekyll", "~> 4.4"  # Use latest 4.x Jekyll version
gem "minima", "~> 2.5"
gem "csv" # Fix Ruby 3.4 CSV issue
gem "tzinfo", "~> 1.2" # Ensure tzinfo is installed

group :jekyll_plugins do
  gem "jekyll-feed", "~> 0.16"  # Latest stable version
  gem "jekyll-seo-tag" # Helps with SEO
  gem "jekyll-sitemap" # Adds a sitemap.xml for SEO
end

# Windows and JRuby does not include zoneinfo files, so bundle the tzinfo-data gem
install_if -> { RUBY_PLATFORM =~ %r!mingw|mswin|java! } do
  gem "tzinfo-data"
end

# Performance booster for watching directories on Windows
gem "wdm", "~> 0.1.1", :install_if => Gem.win_platform?
