---
layout: post
title: "debian-diaspora-notes"
date: 2012-11-17 00:36
comments: true
categories: debian-diaspora
---

## Quilt

Really good tutorial : [Quilt Tutorial - Shakthi Kannan,
April 6, 2006](http://www.shakthimaan.com/downloads/glv/quilt-tutorial/quilt-doc.pdf)

To know which patch you are on : 
```bash
quilt top
```
To move down the series ( to go to older patch)
```bash
quilt push
```
To move up the series ( to go to a newer patch)
```bash
quilt pop
```

The normal state ( code is untouched , all changes in patches )
```bash
$ quilt top
No patches applied
```

## Workflow for patching source with quilt.
Assuming we are in a git repo setup for a git-buildpackage ,

To create a new patch ( to edit a file unrelated to older patch ) :

```bash
quilt new that_failing_test.patch
```

Then let quilt know which files are to be tracked in this patch :

```
quilt add spec/some_spec.rb
```

*Now go ahead and edit the file spec/some_spec.rb*

Run the test runner to verify your modifications fixed the tests.

```
/usr/bin/ruby -I/usr/lib/ruby/vendor_ruby /usr/lib/ruby/vendor_ruby/gem2deb/test_runner.rb
```

```
/usr/bin/ruby1.8 -I/usr/lib/ruby/vendor_ruby /usr/lib/ruby/vendor_ruby/gem2deb/test_runner.rb
```

Once done , update the patch file by 

```
quilt refresh
```

This will update the patch file in patches folder, reflecting the
changes you newly edited.

Unapply all patches, so that our source directory is clean.
Commit the patches (IMPORTANT: Just the patches and the series file)

When it makes more sense to add a change to an older patch we can make
quilt to add changes to that old patch instead of creating a new
patch.

Navigate to the required patch using `` quilt pop `` and `` quilt push
`` .
You might want to do a `quilt pop -a -f` to force unapply all the
patches 

Verify if you are at the required patch by ``quilt top``.When you are
sure about the patch , edit the source code files.When done , update
the patch using 
```
quilt refresh
```
Commit the modified patch file to the git repo and don't forget to ``
git checkout `` the source code file you patched.

## Useful bash aliases

For gem2deb test-runner :

```
alias tr='/usr/bin/ruby -I/usr/lib/ruby/vendor_ruby /usr/lib/ruby/vendor_ruby/gem2deb/test_runner.rb'
```

```
alias 18tr='/usr/bin/ruby1.8 -I/usr/lib/ruby/vendor_ruby /usr/lib/ruby/vendor_ruby/gem2deb/test_runner.rb'
```
