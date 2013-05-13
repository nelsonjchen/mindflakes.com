require 'nanoc/tasks'
require 'stringex'
require 'rubygems'

desc 'Create a new post'
# Nasty nasty hack to support a handful of commas in the title.
# http://stackoverflow.com/questions/7258148/how-can-i-use-a-comma-in-a-string-argument-to-a-rake-task
task :new_post, :title, :title2, :title3, :title4, :title5, :title6, :title7 do |t, args|
  args.with_defaults(:title => 'New Post')
  title = args.to_hash.values.join(', ')
  path =  "./content/blog/posts/#{Time.now.strftime('%Y-%m-%d')}/#{title.to_url}"
  mkdir_p path
  filename = "#{path}/index.md"
  attributes = "#{path}/index.yaml"

  #if File.exist?(filename)
  #  abort('rake aborted!') if ask("#{filename} already exists. Want to overwrite?", %w(y n)) == 'n'
  #end

  puts "Creating new post attributes: #{attributes}"
  open(attributes, 'w') do |post|
    post.puts '---'
    post.puts "title: \"#{title}\""
    post.puts "created_at_time: #{Time.now.strftime('%H:%M:%S %z')}"
    post.puts "---\n\n"
  end
  puts "Creating new post: #{filename}"
  open(filename, 'w') do |post|
    post.puts 'Put your post stuff in here.'
  end
end

