require 'rubygems'
require 'bundler/setup'
Bundler.require(:default)

################################################################################
################## TASKS
################################################################################

task :parse_results, [:limit] do |t, args|
  results = get_results.sort_by! do |result|
    result.time.to_f
  end.reverse

  limit = args.to_hash.fetch(:limit, results.count).to_i

  headings = ["Time", "File", "Class"]

  min_time = results.min_by{|r| r.time}.time
  max_time = results.max_by{|r| r.time}.time

  rows = results[0..limit].map do |result|
    [Paint[result.time, color(min_time, max_time, result.time)], result.file, result.name]
  end

  puts Terminal::Table.new :headings => headings, :rows => rows

  if (results.count > limit)
    print "(#{results.count - limit} truncated)\n"
  end
end

task :parse_results_by_file, [:limit, :sort] do |t, args|
  results_by_file = get_results.group_by{|result| result.file}.map do |file, results|
    time = results.map{|r| r.time}.inject(:+)
    average = time / results.count
    ResultForFile.new(
      file,
      time,
      average,
      results.count
    )
  end.sort_by! do |result|
    sort_type = args.to_hash.fetch(:sort, :time)
    result.send(sort_type)
  end.reverse

  limit = args.to_hash.fetch(:limit, results_by_file.count).to_i

  headings = ["Time", "Average Time", "Test Count", "File"]

  min_time = results_by_file.min_by{|r| r.time}.time
  max_time = results_by_file.max_by{|r| r.time}.time

  rows = results_by_file[0..limit].map do |result|
    [Paint[result.time.round(2), color(min_time, max_time, result.time)],
     Paint[result.average.round(2), color(min_time, max_time, result.average)],
     result.count,
     result.file
    ]
  end

  puts Terminal::Table.new :headings => headings, :rows => rows

  if (results_by_file.count > limit)
    print "(#{results_by_file.count - limit} truncated)\n"
  end
end

################################################################################
################## HELPERS
################################################################################

Result = Struct.new(:class, :name, :file, :time)
ResultForFile = Struct.new(:file, :time, :average, :count)

def get_results
  results = []

  Dir.glob("input/*.xml") do |file|
    doc = Nokogiri::XML(File.open(file))
    results += doc.xpath("//testcase[not(skipped)]").map do |node|
      Result.new(
        node.attributes["classname"].value,
        node.attributes["name"].value,
        node.attributes["file"].value,
        node.attributes["time"].value.to_f,
      )
    end
  end

  results
end

def color(min, max, value)
  weight = (value.to_f - min.to_f) / (max.to_f - min.to_f)

  [255 * weight, 255 * (1 - weight), 0]
end

