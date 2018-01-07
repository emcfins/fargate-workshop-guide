require "haml"
require "listen"
require "pandoc-ruby"
require "time"

require "./helpers"

LAYOUT = "content/layout.haml"

task default: :build

task :build do
  build
end

task :listen do
  listener = Listen.to(".", "content/", only: /\.md|\.haml/) do |modified, added, removed|
    paths = [modified, added, removed].flatten.map { |path|
      ContentFile.new(path).basename
    }.join(",")

    puts "Building -- ts=#{Time.now.iso8601} modified=#{paths}"

    begin
      build
    rescue Exception => ex
      puts ex
      puts ex.backtrace
    end
  end

  listener.start
  sleep
end

task :deploy do
  %x(aws s3 sync site s3://reinvent2017.wildrydes.com/)
end

def build
  Dir["content/*"].each do |path|
    next if path == LAYOUT

    content_file = ContentFile.new(path)
    next if content_file.partial?

    File.open(File.join("site", content_file.stem + ".html"), "w") do |file|
      file.write(render(LAYOUT) { render(path) })
    end
  end
end
