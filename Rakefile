# SPDX-License-Identifier: CC0-1.0

require 'toml'

N = 'libreaudio.toml'
F = File.read N

task default: :check

task check: %w[valid source].map { "check_#{_1}" }

task :check_valid do
  TOML.load F
end

task :check_source do
  # Check if some "source" attributes are missing
  e = F.count '['
  s = F.scan(/^source/).length
  if e != s
    puts "ERROR: #{e} entries != #{s} sources"
    exit -1
  end
end
