# frozen_string_literal: true

source "https://rubygems.org"

# Ruby 버전 명시
ruby "~> 3.1"

# Jekyll 테마
gem "jekyll-theme-chirpy", "~> 7.2", ">= 7.2.1"

# Jekyll 직접 추가
gem "jekyll", "~> 4.3"

gem "html-proofer", "~> 5.0", group: :test

# Windows 플랫폼 전용 gem
group :development do
  if Gem.win_platform?
    gem "tzinfo", ">= 1", "< 3"
    gem "tzinfo-data"
    gem "wdm", "~> 0.2.0"
  end
end
