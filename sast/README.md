# SAST
<!-- TODO: overview of SAST workflows -->


## Examples
### Build Mode `none`
**Language**: `javascript` | `python` | `ruby` | `actions`

```yaml
# .github/workflows/sast.yml
name: SAST

on:
  pull_request:
    branches: [ main ]
  push:
    branches: [ main ]
  workflow_dispatch:

concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: ${{ github.event_name == 'pull_request' || false }}

permissions:
  security-events: write
  packages: read
  actions: read
  contents: read

jobs:
  sast:
    name: SAST
    runs-on: ubuntu-latest
    timeout-minutes: 30
    strategy:
      fail-fast: false

    steps:
      - name: Checkout Repository
        uses: actions/checkout@3d3c42e5aac5ba805825da76410c181273ba90b1 # v7.0.1

        # Optional application setup steps
        #   example: actions/setup-node

      - name: CodeQL init
        uses: braintree/security-workflows/sast/codeql-init@main
        with:
          language: ruby

      - name: CodeQL analyze
        uses: braintree/security-workflows/sast/codeql-analyze@main
        with:
          language: ruby
```


### Build Mode `manual`
**Language**: `java` | `swift` | `go` | `csharp`

```yaml
# .github/workflows/sast.yml
name: SAST

on:
  pull_request:
    branches: [ main ]
  push:
    branches: [ main ]
  workflow_dispatch:

concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: ${{ github.event_name == 'pull_request' || false }}

permissions:
  security-events: write
  packages: read
  actions: read
  contents: read

jobs:
  sast:
    name: SAST
    runs-on: # ubuntu-latest | macos-14-xlarge
    timeout-minutes: 30
    strategy:
      fail-fast: false

    steps:
      - name: Checkout
        uses: actions/checkout@3d3c42e5aac5ba805825da76410c181273ba90b1 # v7.0.1

        # Application setup steps
        #   resolve dependencies, set xcode version, etc
        # 
        # - name: ExampleA
        # - name: ExampleB

      - name: CodeQL init
        uses: braintree/security-workflows/sast/codeql-init@main
        with:
          language: swift
          build-mode: manual

      - name: Build
        shell: bash
        run: |-
          # compile a build for CodeQL to monitor
          # - skip compiling and running tests
          # - include `clean`, exclude build caches

      - name: CodeQL analyze
        uses: braintree/security-workflows/sast/codeql-analyze@main
        with:
          language: swift
```