---
layout: post
title: "gho - github helper"
date: "2015-09-12"
comments: true
categories: 
---

Gho (Github Open) is a shell function :

In zsh :

```bash
gho () {
    open "https://`git config --get remote.origin.url|sed -e s/.git//g|sed s,:,/,g`/$1"
}
```

Or if you're using fish shell. ( You should try [fish](http://fishshell.com/), the autocomplete is nice!) :

```bash
function gho
  set repo (git config --get remote.origin.url|sed -e s/^git@//g|sed -e s/\.git\$//g|sed s,:,/,g)
  open "https://$repo/$argv"
end
```
(Updated this function to a working version)

Make sure `open` command opens a URL. In my linux machine I have aliased `open` to `xdg-open`.

Put this in your `~/.zshrc` or `~/.config/fish/config.fish` for fish shell.

Now with gho, you can :

`gho pulls`  to open pull requests page.

`gho issues` to open issues page.

`gho pull/12` to open pull #12.

`gho something` to open `current directory's repo's URL`/`something`.
