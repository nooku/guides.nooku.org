require "rubygems"
require "tmpdir"

require "bundler/setup"

GITHUB_REPONAME = "nooku/guides.nooku.org"

desc "Generate book files"
task :generate do
  system "gitbook build"
end

desc "Publish to gh-pages"
task :deploy => [:generate] do
Dir.mktmpdir do |tmp|
  sleep 5
  cp_r "build/.", tmp
  Dir.chdir tmp
  system "git init"
  system "git add ."
  message = "Site updated at #{Time.now.utc}"
  system "git commit -m #{message.inspect}"
  system "git remote add origin git@github.com:#{GITHUB_REPONAME}.git"
  system "git push origin master:refs/heads/gh-pages --force"
end
end