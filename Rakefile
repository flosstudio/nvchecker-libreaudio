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

task :lint do
  db = TOML.load F
  db.each do |k, v|
    %w(github gitlab).each { v['source'] = _1 if (v[_1] || !v['source']) }
    if (v['github'] || v['gitlab']) && (!v.has_key? 'unver')
      v['use_max_tag'] = true if (!v.has_key? 'use_max_tag')
      v['prefix'] = 'v' if (!v.has_key? 'prefix')
    end
  end.sort.to_h
  puts TOML::Generator.new(db).body
end
