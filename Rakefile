require 'erb'

task default: %w[article]

task :deploy do
  puts "Deploying to s3"
  system("bundle exec jekyll build")
  system "s3cmd sync --no-mime-magic --guess-mime-type --delete-removed _site/* s3://marco-lopes.com"
end

task :article do
  print "\nEnter article name: "
  article_name = STDIN.gets.chomp
  slug = article_name.gsub(" ", "-")
  date = Time.now
  formatted_date = date.strftime("%Y-%m-%d")
  year = date.strftime("%Y")
  article_file = "articles/_posts/#{year}/#{formatted_date}-#{slug}.md"

  puts "Creating article \"#{article_name}\" in path:\n#{article_file}"
  File.open(article_file, "w+") do |f|
    f.write(ERB.new(File.read("_rake/templates/article.md.erb")).result(binding))
  end
end
