# snapshot-project
Action that will snapshot a GitHub Project 2.0
The tool uploads the project data as a workflow artifact.  These are subject to data retention policies (defaults to 90 days but is confiurable).  If these need to be stored for a longer term then you can download the artifacts in another step and then save them somewhere.

Note:  This relies on timrogers/gh-migrate-project and therefore has the following caveats


### Classic projects are not supported

This tool can't migrate so-called classic Projects - only the newer version of [GitHub Projects](https://docs.github.com/en/issues/planning-and-tracking-with-projects).

Classic Projects can be migrated with [GitHub Enterprise Importer](https://docs.github.com/en/migrations/using-github-enterprise-importer) or [`ghe-migrator`](https://docs.github.com/en/enterprise-cloud@latest/migrations/using-ghe-migrator/about-ghe-migrator).


### Not all data is migrated

The following data is not migrated and will be skipped:

- Views
- The order of project items displayed in your views
- Workflows
- Iteration custom fields
- Draft issues' assignees

### Draft issues will show the person running the migration as the author

Migrated draft issues will show as being created by the person who ran the migration at the time they ran the migration. A note will be prepended to the body with original author login and timestamp.


## Usage
1. Find your Project ID
This can be done by inspecting the URL and grabbing the number

![project-number](https://github.com/Chocrates/snapshot-project/assets/1758164/0343e3d9-3515-4a5f-bdf4-cea72a97e97a)

2. Create your workflow, below is an example on how to do this on demand for a fixed project

```yaml
name: Test Action

on:
  workflow_dispatch:

jobs:
  snapshot:
    runs-on: ubuntu-latest
    steps:
      - uses: chocrates/snapshot-project@main
        with:
          project-owner: chocrates-test-org
          project-id: 11
          pat: ${{ secrets.PAT }}
```


## Restoring a Snapshot
If you find yourself needing to restore your project then you can use the following steps.
You can find the [original steps](https://github.com/timrogers/gh-migrate-project/tree/main?tab=readme-ov-file#step-4-complete-the-repository-mappings-template), these should be considered the source of truth and this README could get out of date depending on updates to the tool

1. Install the gh Migrate Project CLI extension
```
gh extension install timrogers/gh-migrate-project
```
3. Download your archive of the snapshot
4. Unzip your snapshot into your folder
```
[snapshot-project] unzip ~/Downloads/project-11-snapshot-2024-02-15.19.29.35.zip
Archive:  /home/chris/Downloads/project-11-snapshot-2024-02-15.19.29.35.zip
  inflating: project.json            
  inflating: repository-mappings.csv 
```
5. Edit the `repository-mappings.csv` as needed
6. Import the project.  Note this won't let you update an existing project, you will have to import the snapshot into a new project
```
gh migrate-project import --access-token <GitHub PAT> --project-owner <Target Organization> \
  --input-path project.json --repository-mappings-path repository-mappings.csv \
  --project-title "<Title of new Project>"
```
