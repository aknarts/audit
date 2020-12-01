#!/usr/bin/env rake

# Import other external rake tasks
Dir.glob('tasks/*.rake').each { |r| import r }

# Style tests with Cookstyle
namespace :style do
  begin
    require 'cookstyle'
    require 'rubocop/rake_task'

    desc 'Run Ruby style checks'
    RuboCop::RakeTask.new(:ruby)
  rescue LoadError => e
    puts ">>> Gem load error: #{e}, omitting style:ruby" unless ENV['CI']
  end
end

desc 'Run all style checks'
task style: ['style:chef', 'style:ruby']
task lint: ['style']

# ChefSpec
begin
  require 'rspec/core/rake_task'

  desc 'Run ChefSpec examples'
  RSpec::Core::RakeTask.new(:spec)
rescue LoadError => e
  puts ">>> Gem load error: #{e}, omitting spec" unless ENV['CI']
end

namespace :test do
  task :integration do
    concurrency = ENV['CONCURRENCY'] || 1
    os = ENV['OS'] || ''
    sh('sh', '-c', "kitchen test -c #{concurrency} #{os}")
  end

  # call it like this: rake test:kitchen_automate[verify]
  task :kitchen_automate, :action do |_t, args|
    if %w(list create converge verify destroy test).include?(args[:action])
      sh('sh', '-c', "cd test/kitchen-automate; kitchen #{args[:action]}")
    else
      puts ">>> Unknown kitchen action: #{args[:action]}"
    end
  end
end

# Default
task default: %w(style spec)
