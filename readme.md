# Introduction
This is for parsing the output from the junit formatter for rspec for the purposes of finding slow tests.

## Usage
* Place your xml files in the `input/` dir with `.xml` extensions.
* run `rake parse_results[<limit>]` to get the sorted results output to console as a table.
* run `rake parse_results_by_file[<limit>,<sort>]` to get the sorted results grouped by file output to console as a table.
