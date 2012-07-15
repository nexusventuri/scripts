require 'erb'
require 'rake'

namespace :project do
  desc "Create an empty project with a default gemfile and rvmrc"
  task :create_sinatra, [:projectname, :base_dir] => [:create_rvmrc, :generate_sinatra_gemfile]

  desc "Create an rvmrc for the project"
  task :create_rvmrc, :projectname, :base_dir do |t, args|
    template = %q{
#!/bin/bash

rvm use ruby-1.9.3@<%= projectname %> --create
bundle install
if [ $? != 0 ]; then
  gem install bundler
  bundle install
fi
    }

    write_template(template, '.rvmrc', args)
  end


  desc "Create an empty template for the project" 
  task :generate_sinatra_gemfile, :projectname, :base_dir do |t, args|
    template = %q{
source 'http://rubygems.org'

gem 'rake'
gem 'nokogiri'
gem 'sinatra'
gem 'shotgun'
    }

    write_template template, 'Gemfile', args
  end


  def write_template(template, filename, args) 
    projectname = args[:projectname]
    basedir = args[:base_dir]
    directory(basedir)

    template = ERB.new template.strip

    body = template.result(binding)
    rvmrc = File.join(basedir, filename)

    File.open(rvmrc, 'w'){|file| file.write(body) }
  end
end

