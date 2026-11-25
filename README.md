# MCP Guardian GitHub Action

Run [MCP Guardian](https://github.com/hanzales/mcp-guardian) against an MCP configuration in GitHub Actions. The action downloads a pinned self-contained release binary, emits text/JSON/SARIF output, and fails the job when the configured severity threshold is reached.

## Usage

```yaml
name: MCP security
on:
  pull_request:
permissions:
  contents: read
  security-events: write
jobs:
  scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: hanzales/mcp-guardian-action@v1
        with:
          config: .mcp.json
          version: 0.6.0-preview.3
          fail-on: high
          format: sarif
          output: artifacts/mcp-guardian.sarif
      - uses: github/codeql-action/upload-sarif@v3
        if: always()
        with:
          sarif_file: artifacts/mcp-guardian.sarif
```

The action supports Linux x64, macOS x64/arm64, and Windows x64 runners. It does not execute MCP servers or call advertised tools; it only scans the selected configuration.

## Inputs

| Input | Default | Description |
| --- | --- | --- |
| `config` | required | MCP configuration path |
| `version` | `0.6.0-preview.3` | Release version without `v` |
| `fail-on` | `high` | `critical`, `high`, `medium`, `low`, or `none` |
| `format` | `text` | `text`, `json`, or `sarif` |
| `output` | empty | Optional report path |
| `working-directory` | `.` | Scan working directory |

Pin a specific release version in production workflows. Review the MCP Guardian security model and limitations before relying on the action as a CI gate.

The action also writes a compact result to the GitHub Actions job summary when
`GITHUB_STEP_SUMMARY` is available. SARIF output can still be uploaded to code
scanning with `github/codeql-action/upload-sarif`.

## License

MIT. See [LICENSE](LICENSE).
