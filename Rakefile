require 'erb'
require 'json'
require 'pp'

SRC_DIR = %|./src|
BOOT    = %W| class |
PARSER  = %W| tokens parsers jsml cli |
VERSION = File.read("./VERSION").strip;

task :test => :compile do
  require "./platforms/gem/lib/mochiscript"
  files = ENV['TEST'] ? [ "./tests/#{ENV['TEST']}.ms" ] : Dir['./tests/*.ms']

  files.each do |f|
    puts "Testing: " + f
    ctx = Mochiscript::Context.new
    begin
      ctx.eval_ms(File.read(f))
    rescue Exception => e
      puts "Error: " + ctx.parse(File.read(f))
      puts "TREE:\n" + ctx.pp(File.read(f))
      puts e.to_s
    end
  end
end

task :compile do
  @boot   = BOOT.collect { |f| parse("#{SRC_DIR}/#{f}.ms") }.join("\n")
  @parser = PARSER.collect { |f| parse("#{SRC_DIR}/#{f}.ms") }.join("\n")

  { 
    'boot.js.erb' => './platforms/gem/vendor/assets/javascripts/mochiscript.js',
    'core.rb.erb' => './platforms/gem/lib/mochiscript/core.rb',
    'mochiscript.rb.erb' => './platforms/gem/lib/mochiscript.rb',

    'node.js.erb' => './platforms/npm/lib/mochiscript/mochiscript.js',
    'package.json.erb' => './platforms/npm/lib/package.json'
  }.each_pair do |target, destination|
    target = "./src/platforms/#{target}"
    puts "Writing #{target} to #{destination}"
    File.open(destination, "w") { |t| t << ERB.new(File.read(target)).result(binding) }
  end
end

def parse(file)
  `js2 render #{file}`
end
