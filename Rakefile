require "bundler/gem_tasks"

datatables_dir = "DataTablesSrc" 
extensions = %w(AutoFill Buttons ColReorder FixedColumns FixedHeader KeyTable Responsive RowReorder Scroller Select RowGroup)
frameworks = %w(regular bootstrap bootstrap4 foundation jqueryui material semanticui uikit)

namespace :images do

  desc "Delete images directory"
  task :clean do
   tgt_dir = "app/assets/media/datatables/"
   rm_rf tgt_dir
  end
  
  desc "Copy images from #{datatables_dir}/media/images/"
  task :copy do
    tgt_dir = "app/assets/images/datatables/"
    mkdir_p tgt_dir
    Dir.glob("#{datatables_dir}/media/images/*.png") do |src_file|
      cp src_file, tgt_dir
    end
  end

  desc "Setup image assets"
  task setup: [:clean, :copy]
end

namespace :javascripts do
  
  desc "Cleaning javascripts directory"
  task :clean do
   rm_rf "app/assets/javascripts/datatables"
  end
  
  desc "Copy #{datatables_dir}/media/js/"
  task :copy do
    src_dir = "#{datatables_dir}/media/js/."
    tgt_dir = "app/assets/javascripts/datatables/"
    mkdir_p tgt_dir
    cp_r src_dir, tgt_dir
  end
  
  desc "Copy #{datatables_dir}/extensions/*/js"
  task :copy_extensions do
    extensions.each do |ext|
      src_dir = "#{datatables_dir}/extensions/#{ext}/js/."
      tgt_dir = "app/assets/javascripts/datatables/extensions/#{ext}/"
      mkdir_p tgt_dir
      cp_r src_dir, tgt_dir
    end
  end

  desc "Setup javascript assets"
  task setup: [:clean, :copy, :copy_extensions]
end

namespace :stylesheets do
  desc "Cleaning stylesheets directory"
  task :clean do
   rm_rf "app/assets/stylesheets/datatables"
  end

  desc "Copy #{datatables_dir}/media/css/"
  task :copy do
    src_dir = "#{datatables_dir}/media/css/."
    tgt_dir = "app/assets/stylesheets/datatables/"
    mkdir_p tgt_dir
    cp_r src_dir, tgt_dir
  end
  
  desc "Copy #{datatables_dir}/extensions/*/css"
  task :copy_extensions do
    extensions.each do |ext|
      src_dir = "#{datatables_dir}/extensions/#{ext}/css/."
      tgt_dir = "app/assets/stylesheets/datatables/extensions/#{ext}/"
      mkdir_p tgt_dir
      cp_r src_dir, tgt_dir
    end
  end

  desc "Fix image URLs in stylesheets"
  task :fix_urls do
    Dir.glob('app/assets/stylesheet/datatables/*.css').each do |tgt_file|
      content = File.read(tgt_file)
      fixed_content = content.gsub(/url\(\"\.\.\/images\/([A-Za-z_]*.png)\"\)/, 'image-url("\1")')
      File.open(tgt_file, "w") { |f| f.puts fixed_content}
    end
  end

  desc "Setup stylesheet assets"
  task setup: [:clean, :copy, :copy_extensions, :fix_urls]
end

desc "Remove minified file .min"
task :cleanup do
  Dir.glob('app/assets/**/*.min.*').each do |file|
    rm file
  end
  rm "app/assets/javascripts/datatables/jquery.js"
end

task :templates do
  templates_dir = "lib/generators/jquery/datatables/templates"
  rm_rf templates_dir
  mkdir_p templates_dir
  stylesheets = Dir.glob('app/assets/stylesheets/**/*')
  javascripts = Dir.glob('app/assets/javascripts/**/*')
  
  frameworks.each do |framework|
    tgt_css_file = "#{templates_dir}/#{framework}.css.tt"
    tgt_js_file = "#{templates_dir}/#{framework}.js.tt"
    
    
    javascripts.each do |file|
      file_name = file.gsub("app/assets/javascripts/", "")
      File.open(tgt_js_file, "a") { |f| f.puts "//=require #{file_name}"} if file_name.match(/jquery\./)
    end
      
    javascripts.each do |file|
      file_name = file.gsub("app/assets/javascripts/", "")
      next if frameworks.any? { |s| file_name.match(/#{Regexp.escape(s)}/) } or file_name.match(/jquery/)
      File.open(tgt_js_file, "a") { |f| f.puts "// require #{file_name}"} if file_name.match(/dataTables/)
    end
    
    
    case framework
      
    when "bootstrap", "bootstrap4", "foundation", "jqueryui", "material", "semanticui", "uikit"
      
      stylesheets.each do |file|
        file_name = file.gsub("app/assets/stylesheets/", "")
        File.open(tgt_css_file, "a") { |f| f.puts "*= #{file_name}"} if file_name.match(/#{Regexp.escape(framework)}\./)
      end
      
      javascripts.each do |file|
        file_name = file.gsub("app/assets/javascripts/", "")
        File.open(tgt_js_file, "a") { |f| f.puts "//=require #{file_name}"}  if file_name.match(/#{Regexp.escape(framework)}\./)
      end
      
    else
    
      #core first]
      stylesheets.each do |file|
        file_name = file.gsub("app/assets/stylesheets/", "")
        File.open(tgt_css_file, "a") { |f| f.puts "*=require #{file_name}"} if file_name.match(/jquery\./)
      end
      
      #plugins
      stylesheets.each do |file|
        file_name = file.gsub("app/assets/stylesheets/", "")
        next if frameworks.any? { |s| file_name.match(/#{Regexp.escape(s)}/) } or file_name.match(/jquery/)
        File.open(tgt_css_file, "a") { |f| f.puts "*=require '#{file_name}';"}  if file_name.match(/dataTables/)
      end
      
    
      
    end
    
  end
    
 
  
end

desc "Setup or update assets files"
task setup: ["images:setup", "javascripts:setup", "stylesheets:setup"]
