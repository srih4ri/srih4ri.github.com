---
layout: post
title: "Setup for learning HAML"
date: 2012-12-19 22:58
comments: true
categories: 
---

Install the gems `haml` , `guard-haml` , `guard-livereload` and [LiveReload chrome extension](https://chrome.google.com/webstore/detail/livereload/jnihajbhpnppcggbcgedagnkighmdlei)

Create a Guard file that will compile the haml files you create and reload html files in chrome :


```
guard 'haml', :run_at_start => true do
  watch(%r{^.+(\.haml)$})
end

guard 'livereload' do
  watch(%r{^.+(\.html)$})
end

```

Edit index.haml while watching index.html reloaded live in the browser.
