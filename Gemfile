source "https://rubygems.org"
# gem "jekyll", "~> 4.1.0"
gem "minima", "~> 2.5.1"
# gem "i18n", "~> 1.8.3"
gem "github-pages", group: :jekyll_plugins

group :jekyll_plugins do
  gem "jekyll-feed", "~> 0.13.0"
  gem "jekyll-seo-tag", "~> 2.6.1"
end

install_if -> { RUBY_PLATFORM =~ %r!mingw|mswin|java! } do
  gem "tzinfo", "~> 2.0.2"
  gem "tzinfo-data"
end

gem "wdm", "~> 0.1.1", :install_if => Gem.win_platform?

