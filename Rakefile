

desc "Create a new note"
task :note do
  note_name =ENV['note'] || 'misc'
  note_file = "notes/#{note_name}.md"
  `touch #{note_file}`
end

desc "Create a new post : env POST='My new blogpost' rake np"
task :np do
  abort "Post title is blank, pass as ENV['post']" if ENV['POST'].nil?
  title = ENV['POST']
  post_name = ENV['POST'].gsub(/[^\w]+/,"-").downcase
  posts_folder = "_posts"
  post_date = Time.now.strftime("%Y-%m-%d")
  post_time = Time.now.strftime("%Y-%m-%d %H:%M")

  front_matter = <<-FRONT_MATTER
---
layout: post
title: #{title}
date: #{post_time}
comments: true
categories: uncategorized
---
  FRONT_MATTER

  post_file = "#{posts_folder}/#{post_date}-#{post_name}.md"
  puts "Creating post #{post_file} with\n #{front_matter}"
  File.open(post_file, 'w'){|f| f.write(front_matter)}
  `open #{post_file}`
end
