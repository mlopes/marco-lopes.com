require 'erb'

task :deploy do
  puts "Deploying to s3"
  system("bundle exec jekyll build")
  system "s3cmd sync --no-mime-magic --guess-mime-type --delete-removed _site/* s3://marco-lopes.com"
end

