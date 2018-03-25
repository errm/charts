# Helm Repo

This is the source for my personal helm repo.

After editing anything in a chart:
* update the version number in `Chart.yaml`
* run `rake` to package the new version
* add the generated files from `docs/` to git
