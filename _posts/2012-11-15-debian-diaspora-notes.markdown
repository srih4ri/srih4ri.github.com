---
layout: post
title: "#debian-diaspora packaging notes"
date: 2012-11-15 23:38:20 +0530
published: true
categories: debian-diaspora
comments: true
---

## Missing diff-lcs

Error :

``
/usr/lib/ruby/vendor_ruby/rspec/expectations/differ.rb:1:in `require': no such file to load -- diff/lcs (LoadError)
``

Fix :

``
sudo apt-get install ruby-diff-lcs
``
