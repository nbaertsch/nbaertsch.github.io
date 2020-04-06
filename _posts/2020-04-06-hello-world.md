---
title: Hello World
layout: post
encrypted_text: true
---

#Jekyll Setup
> Hello world, heres how to build a Jekyll site like this one.


##Ruby Envioronment

Dependencies
```sh
sudo apt-get install ruby-full build-essential zlib1g-dev
```

Gem installation env vars
```sh
echo '# Install Ruby Gems to ~/gems' >> ~/.zshrc
echo 'export GEM_HOME="$HOME/gems"' >> ~/.zshrc
echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

Install Jekyll
```sh
gem install jekyll bundler
```


Theme is [Project-Negya](http://jekyllthemes.org/themes/project-negya/)

Decrypt with this key: 1234567890123456
<p class = "encrypted" id = "Q32hJFHSVLD6rSqnahJA9WxVj9koFEThsvclAnFq97g=">
Encrypted text.
</p>