---
layout: post
title: "Mac"
date: 2016-08-22 02:04
categories: notes
---

# Update Screenshot folder 

```bash
mkdir ~/Pictures/ScreenShots
defaults write com.apple.screencapture location /Users/srihari/Pictures/ScreenShots/
killall SystemUIServer # Changes applied only after this
```
