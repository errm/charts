require "ostruct"
require "yaml"
require "erb"

REPO_URL = "https://errm.github.io/charts"

class Chart < OpenStruct
  def initialize(source)
    @to_str = source
    super(YAML.load_file(source))
  end

  def self.all
    @all ||= Dir["**/Chart.yaml"].map { |source| new(source) }
  end

  def self.targets
    all.map(&:target)
  end

  def self.from_target(target)
    all.detect { |chart| target == chart.target }
  end

  attr_reader :to_str

  def target
    "docs/#{name}-#{version}.tgz"
  end
end

desc "Build packaged helm charts"
task package: Chart.targets

rule ".tgz" => ->(target) { Chart.from_target(target) } do |t|
  sh "helm package -d docs #{t.source.name}"
end

desc "Index the helm repo"
task index: ["docs/index.yaml", "docs/index.html"]
rule "docs/index.yaml" => Chart.targets do
  sh "helm repo index docs --url #{REPO_URL}"
end

rule "docs/index.html" => Chart.all + ["index.html.erb"] do |t|
  File.write(t.name, ERB.new(File.read("index.html.erb")).result)
end

task default: [:package, :index]
