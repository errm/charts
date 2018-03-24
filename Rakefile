require "yaml"
require "erb"

SOURCE_FILES = Dir["**/Chart.yaml"]
CHARTS = SOURCE_FILES.map { |f| YAML.load_file(f) }
PACKAGED_CHARTS = CHARTS.map { |chart| "docs/#{chart["name"]}-#{chart["version"]}.tgz" }
HTML_INDEX = "docs/index.html"

task default: :package
task package: PACKAGED_CHARTS + [:html]

task :html do
  template = ERB.new(File.read(HTML_INDEX + ".erb"))
  File.write(HTML_INDEX, template.result(binding))
end

rule ".tgz" => ->(package) { SOURCE_FILES.detect { |source| source.match(package.match(/docs\/(\w+).*\.tgz/)[1]) } } do |t|
  puts t.source
  chart = YAML.load_file(t.source)
  sh "helm package #{chart["name"]}"
  sh "mv #{chart["name"]}-#{chart["version"]}.tgz docs/"
  sh "helm repo index docs --url https://errm.github.io/charts"
end
