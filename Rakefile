require 'rubygems'
require 'bundler/setup'
Bundler.require(:default)

Result = Struct.new(:class, :name, :file, :time)

task :parse_results, [:limit] do |t, args|
  results = []

  Dir.glob("input/*.xml") do |file|
    doc = Nokogiri::XML(File.open(file))
    results += doc.xpath("//testcase[not(skipped)]").map do |node|
      Result.new(
        node.attributes["classname"].value,
        node.attributes["name"].value,
        node.attributes["file"].value,
        node.attributes["time"].value,
      )
    end
  end

  results = results.sort_by! do |result|
    result.time.to_f
  end.reverse

  limit = args.to_hash.fetch(:limit, results.count).to_i

  headings = ["Time", "File", "Class"]

  min_time = results.min{|r| r.time.to_f}.time.to_f
  max_time = results.max{|r| r.time.to_f}.time.to_f

  rows = results[0..limit].map do |result|
    [Paint[result.time, color(min_time, max_time, result.time)], result.file, result.name]
  end

  puts Terminal::Table.new :headings => headings, :rows => rows

  if (results.count > limit)
    print "(#{results.count - limit} truncated)\n"
  end
end

def color(min, max, value)
  weight = (value.to_f - min.to_f) / (max.to_f - min.to_f)

  [255 * (1 - weight), 255 * weight, 0]
end
