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

  rows = results[0..limit].map do |result|
    [result.time, result.file, result.name]
  end

  puts Terminal::Table.new :headings => headings, :rows => rows

  if (results.count > limit)
    print "(#{results.count - limit} truncated)\n"
  end
end
