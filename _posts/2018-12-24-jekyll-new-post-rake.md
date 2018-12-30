---
layout: post
title: "Jekyll new post rake task"
date: "2016-09-17"
comments: true
categories: jekyll
---

Add this to your Rakefile : 

```
task :np do
  note_name =ENV['note'] || 'misc'
  note_file = "_posts/#{Time.now.to_i}-#{note_name}.md"
  `touch #{note_file}`
end
```

Create a new post like so:

```
 env note="Jekyll new post rake task" rake np
```

Now you can edit your post at : `_posts/2018-12-24-jekyll-new-post-rake.md`
