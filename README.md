# Setup the atopile CLI in Github Actions

A Github action to run atopile CLI ⚙️🚀

## Configuration

Use this action to run `ato` commands within your GitHub Actions workflows. You can specify the `atopile` version explicitly or derive it from a configuration file.

```yaml .github/workflows/main.yml
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: atopile/setup-atopile@v1
        with:
          # Either: derive the version from a config file (e.g., pyproject.toml or ato.yaml)
          # [Recommended and default if neither are provided]
          ato-config: "ato.yaml"
          # Or: Specify the atopile version directly
          version: "0.3.23"
          # Optionally: specify a working directory to look for ato.yaml
          working-directory: "packages/my-package"

      # Do something!
      - run: ato --version
```

## Using a Pre-built Branch Image

The atopile CI automatically builds and publishes Docker images for branches, PRs, and commits. To use one of these pre-built images, specify the `docker-tag`:

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: atopile/setup-atopile@v1
        with:
          # Use a pre-built image from a branch (note: '/' becomes '-')
          docker-tag: "feature-fabll_part2"

      - run: ato build
```

### Available Docker Tags

The atopile CI publishes images with these tag formats:

| Source     | Tag Format                       | Example                       |
| ---------- | -------------------------------- | ----------------------------- |
| Branch     | `<branch-name>` (with `/` → `-`) | `feature-fabll_part2`, `main` |
| PR         | `pr-<number>`                    | `pr-123`                      |
| Commit SHA | `sha-<short-sha>`                | `sha-abc1234`                 |
| Release    | `<version>`                      | `0.14.0`, `0.13.5`            |

**Note:** When `docker-tag` is specified, `version` and `ato-config` are ignored.

## Working with Multiple Packages

You can use this action with multiple packages in different directories:

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        package: [package1, package2, package3]
    steps:
      - uses: actions/checkout@v4
      - uses: atopile/setup-atopile@v1
        with:
          working-directory: ${{ matrix.package }}
      - run: ato sync
        working-directory: ${{ matrix.package }}
      - run: ato build
        working-directory: ${{ matrix.package }}
```

## Inputs

| Input               | Description                                                        | Required | Default |
| ------------------- | ------------------------------------------------------------------ | -------- | ------- |
| `ato-config`        | Path to ato.yaml config file                                       | No       | `""`    |
| `version`           | Specific atopile version to use                                    | No       | `""`    |
| `docker-tag`        | Pre-built Docker image tag (e.g., `feature-fabll_part2`, `pr-123`) | No       | `""`    |
| `working-directory` | Working directory for ato.yaml lookup                              | No       | `.`     |

See the [action.yml](action.yml) for full details.
