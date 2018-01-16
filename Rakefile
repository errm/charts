require "yaml"
SOURCE_FILES = Dir["**/Chart.yaml"]
CHARTS = SOURCE_FILES.map { |f| YAML.load_file(f) }
PACKAGED_CHARTS = CHARTS.map { |chart| "docs/#{chart["name"]}-#{chart["version"]}.tgz" } + ["docs/index.yaml"]

task default: :package
task package: PACKAGED_CHARTS

rule ".tgz" => SOURCE_FILES do |t|
  chart = YAML.load_file(t.source)
  sh "helm package #{chart["name"]}"
  sh "mv #{chart["name"]}-#{chart["version"]}.tgz docs/"
  sh "helm repo index docs --url https://errm.github.io/charts"
end
