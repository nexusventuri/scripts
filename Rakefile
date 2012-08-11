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

    File.open(rvmrc, File::WRONLY|File::CREAT){|file| file.write(body) }
  end

  def puts_list(title, list)
    if(!list.empty?)
      puts title
      puts ("-" * title.length)
      puts
      list.each_line{|line| puts "* #{line}"}
      puts
    end
  end

  namespace :git do
    desc "Given a folder checks subfolders for un-pushed changes"
    task :find_un_changes, :base_dir do |t, args|
      dirs = Dir.glob(File.join(args[:base_dir], "*/")).find_all{|x| File.directory?(x)}

      dirs.each do |folder|
        unpushed_changes = `cd "#{folder}"; git log --pretty=oneline origin/master..HEAD 2> /dev/null`
        local_changes = `cd "#{folder}"; git status -s 2> /dev/null`
        if !unpushed_changes.empty? || !local_changes.empty?
          puts folder
          puts ("=" * folder.length)
          puts

          puts_list("Unpushed changes", unpushed_changes)

          puts_list("Local changes ", local_changes)
          puts
          puts
        end
      end
    end

  end
end

