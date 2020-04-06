---
title: Hello World
layout: post
---

# Jekyll Setup
>
> Hello world, heres how to build a Jekyll site like this one.
>
<br/>

## Ruby Envioronment

Dependencies
```sh
sudo apt-get install ruby-full build-essential zlib1g-dev
```
<br/>

Gem installation env vars
```sh
echo '# Install Ruby Gems to ~/gems' >> ~/.zshrc
echo 'export GEM_HOME="$HOME/gems"' >> ~/.zshrc
echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```
<br/>

Install Jekyll
```sh
gem install jekyll bundler
```
<br/>

Theme is [Project-Negya](http://jekyllthemes.org/themes/project-negya/)
