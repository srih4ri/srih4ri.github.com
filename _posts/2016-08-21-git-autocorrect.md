---
layout: post
title: "Turning on git autocorrect"
date: "2016-08-21"
comments: true
categories: git
---

To turn on autocorrect feature of `git`,
run :

```bash
git config --global help.autocorrect 10
```

After setting this, if you make a mistake in 
git commands, git autocorrects it. Example : 

```bash
~/C/srih4ri.github.com (master ☡=) git addd .
WARNING: You called a Git command named 'addd', which does not exist.
Continuing under the assumption that you meant 'add'
in 1.0 seconds automatically...
```

The '10' in the end says git will wait for `1` second ( 10 * 0.1 second )
before it auto corrects and proceeds with the command. If you need git to wait longer, 
say 5 seconds( 50 * 0.1 seconds ), run `git config --global help.autocorrect 50`.
