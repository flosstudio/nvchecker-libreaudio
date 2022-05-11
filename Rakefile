# frozen_string_literal: true

# SPDX-License-Identifier: CC0-1.0

require 'opml-parser'
require 'toml'

N = 'libreaudio.toml'
F = File.read(N).freeze

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
    exit(-1)
  end
end

task :lint do
  db = TOML.load F
  db.each do |_k, v|
    %w[github gitlab].each { v['source'] = _1 if v[_1] || !v.key?('source') }
    if (v['github'] || v['gitlab']) && (!v.key? 'unver')
      v['use_max_tag'] = true unless v.key? 'use_max_tag'
      v['prefix'] = 'v' unless v.key? 'prefix'
    end
  end.sort.to_h
  puts TOML::Generator.new(db).body
end

task :opml do
  include OpmlParser
  OpmlParser::Outline.new
  db = TOML.load F
  feeds = db.reduce([]) do |a, (_k, v)|
    if v.key? 'github'
      a << { xmlUrl:
             File.join('https://github.com', v['github'], 'tags.atom') }
    elsif v.key? 'gitlab'
      url = if v['gitlab'].start_with?('http')
              v['gitlab']
            else
              File.join 'https://gitlab.com', v['gitlab']
            end
      a << { xmlUrl: File.join(url, '/-/tags?format=atom') }
    end
    a
  end
  outlines = feeds.map { OpmlParser::Outline.new _1 }
  opml = OpmlParser.export outlines, 'LibreAudio Tags'
  f = File.open 'subscriptions.xml', 'w'
  f.write(opml)
  f.close
end
