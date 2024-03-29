# frozen_string_literal: true

require "bundler/gem_tasks"
require "rake/testtask"
require "rubocop/rake_task"
require_relative "lib/brew_lib"

Rake::TestTask.new(:test) do |t|
  t.libs << "test"
  t.libs << "lib"
  t.test_files = FileList["test/**/test_*.rb"]
end

RuboCop::RakeTask.new

def version
  `git describe --tags --abbrev=0`.chomp
end

desc "Create a new release, push and system install [patch|minor|major], default is patch"
task bump: [:test] do |_, args|
  part = args.to_a.fetch(0, "patch")
  sh "{ git add -A && git commit --quiet -m 'bump #{part}' >/dev/null &&
          gem bump --silent --quiet --tag --push --release &&
          gh release create $(git describe --tags --abbrev=0) --generate-notes &&
          sleep 10; gem install brew_lib -v $(git describe --tags --abbrev=0 | sed 's/v//g'); } || true"
end

task default: %i[test rubocop]

desc "Clean all system installed gems and install gems"
task :setup do
  sh "bundle clean --force || true"
  sh "gem uninstall --ignore-dependencies --all -x"
  sh "gem install bundle bundler irb"
  sh "bundle install"
end

desc "Show tasks in current Rakefile, to see descriptions rake -T or rake -D"
task :tasks do
  sh "rake", "--tasks"
end
