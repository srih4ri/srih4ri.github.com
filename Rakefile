task :note do
  note_name =ENV['note'] || 'misc'
  note_file = "notes/#{note_name}.md"
  `touch #{note_file}`
end

task :np do
  note_name =ENV['note'] || 'misc'
  note_file = "_posts/#{Time.now.to_i}-#{note_name}.md"
  `touch #{note_file}`
end
