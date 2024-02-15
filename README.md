# snapshot-project
Action that will snapshot a GitHub Project 2.0

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
