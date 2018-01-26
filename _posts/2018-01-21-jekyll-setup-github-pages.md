---
layout: post
title:  "How did I setup this website"
date:   2018-01-21 22:19:41 -0800
categories: technical github-pages jekyll
author: Vivek Bharadwaj
---
In this post I will tell you the step by step process of setting up a github pages site taking this current site as an example and reference.
Here's the deal, github will let you host a static site on their servers. So, why not make one. To focus on creating content, we will use a static site generator ala [Jekyll][jekyll-home]{:target="_blank"}. Instead of html and css, we will write our posts and pages in `Markdown` and let jekyll generate a static site that can be hosted on any web server. Here's a quick and easy [tutorial][markdown-tutorial]{:target="_blank"} I used to get up and running: it's simple and probably takes 15 minutes.

#### 1. Setup Ruby
Jekyll is based on Ruby, so lets get that installed. I use a Mac and it comes pre installed with Ruby. So yay!

#### 2. Collect the Gems
`gem` or `RubyGems` is a package manager for Ruby. The site has a well documented install process. Follow it and you should be good to go.

#### 3. Setting up our site directory
Create a directory for your website. This will house the git repo that will host the markdowns, build files, etc.
    
Make a `Gemfile` and add these gems to it

```bash
source "https://rubygems.org"
gem "jekyll", "~> 3.6.2"

gem "github-pages", group: :jekyll_plugins

group :jekyll_plugins do
  gem "jekyll-feed", "~> 0.6"
end

gem 'tzinfo-data', platforms: [:mingw, :mswin, :x64_mingw, :jruby]

gem "kramdown"
gem "rouge"
gem "jekyll-remote-theme"
gem "jekyll-seo-tag"
gem "jekyll-sitemap"
gem "jekyll-mentions"
gem "jekyll-paginate"
gem "jekyll-redirect-from"
gem "jekyll-default-layout"
gem "jemoji"
```

This is sufficient to make install jekyll and other jekyll-plugins.
To install, run
```bash
bundle install
```

Note: If you face an issue during this install related to `nokogiri` missing xml libraries, it's likely that you are missing libxml2. 
Install it with brew and force link it.
After that, you can install the offending gem by instructing it to use the just installed libxml2 like this: the path or gem might change, but this is the most likely cause of error.
```bash
sudo gem install nokogiri -- --use-system-libraries --with-xml2-include=/usr/local/opt/libxml2/include --with-xml2-lib=/usr/local/opt/libxml2/lib
```

#### 4. Configure this site
Take a look at the source for this [site][this-github] and observe the _config.yml file. 
This file contains all the configurations that jekyll will look at to generate the site.
I will only highlight interesting points in this file to get you started off
```yaml
markdown: kramdown
highlighter: rouge

kramdown:
  syntax_highlighter: rouge
  syntax_highlighter_opts:
    css_class: 'highlight'
    span:
      line_numbers: false
    block:
      line_numbers: true
```
`kramdown` is basically markdown with some extensions. `rouge` is a syntax highlighter that is highlighting any code snippets you see.

```yaml
plugins:
  - jekyll-feed
  - jekyll-remote-theme
  - jekyll-seo-tag
  - jekyll-redirect-from
  - jekyll-default-layout
  - jekyll-feed
  - jemoji

remote_theme: daviddarnes/alembic

```
This is a list of plugins with the most important being `jekyll-remote-theme`. This lets us use a theme available on github and apply to our site. We need to mention what theme to use: `remote_theme` gives that information.
You can check out the [source][this-github] and see how to tinker with jekyll site.

More to follow... 


[jekyll-home]: https://jekyllrb.com/
[markdown-tutorial]: https://www.markdowntutorial.com
[rubygems-home]: https://rubygems.org/
[this-github]: https://github.com/bvivek35/bvivek35.github.io/