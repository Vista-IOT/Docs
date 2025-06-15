source "https://rubygems.org"

# GitHub Pages gem includes Jekyll and supported plugins
gem "github-pages", group: :jekyll_plugins

# Optional plugins for enhanced functionality
group :jekyll_plugins do
  gem "jekyll-feed"
  gem "jekyll-sitemap"
  gem "jekyll-seo-tag"
end

# Windows and JRuby compatibility
install_if -> { RUBY_PLATFORM =~ %r!mingw|mswin|java! } do
  gem "tzinfo", "~> 1.2"
  gem "tzinfo-data"
end

# Performance booster for watching directories
gem "wdm", "~> 0.1.1", :install_if => Gem.win_platform?

