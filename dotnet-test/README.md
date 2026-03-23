# dotnet-test Action

A GitHub Action to execute unit tests for .NET projects.

## Description

This action executes `dotnet test` command with specified parameters to run unit tests for .NET projects. It supports glob patterns for test project selection, provides detailed test results, and optionally collects code coverage.

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------||
| `projects` | Glob pattern for test project files (e.g., `**/*Tests/*.csproj`) | Yes | - |
| `configuration` | Test configuration (e.g., `Release`, `Debug`) | Yes | - |
| `working-directory` | Working directory for the operation | No | `.` |
| `verbosity` | Verbosity level (`quiet`, `minimal`, `normal`, `detailed`, `diagnostic`) | No | `minimal` |
| `no-build` | Skip building before testing | No | `false` |
| `collect-coverage` | Collect code coverage | No | `false` |
| `logger` | Test logger format | No | `console` |

## Outputs

| Output | Description |
|--------|-------------|
| `success` | Boolean indicating if all tests passed |
| `tests-total` | Total number of tests executed |
| `tests-passed` | Number of tests that passed |
| `tests-failed` | Number of tests that failed |
| `tests-skipped` | Number of tests that were skipped |
| `coverage-report-path` | Path to coverage report (if enabled) |

## Usage

### Basic Usage

```yaml
- name: Test
  uses: ./.github/actions/dotnet-test
  with:
    projects: '**/*Tests/*.csproj'
    configuration: 'Release'
```

### Advanced Usage

```yaml
- name: Test with coverage
  uses: ./.github/actions/dotnet-test
  with:
    projects: 'tests/**/*.csproj'
    configuration: 'Debug'
    working-directory: './MyProject'
    verbosity: 'normal'
    no-build: 'true'
    collect-coverage: 'true'
    logger: 'trx'
```

### Using Outputs

```yaml
- name: Test
  id: test
  uses: ./.github/actions/dotnet-test
  with:
    projects: '**/*Tests/*.csproj'
    configuration: 'Release'

- name: Check test results
  run: |
    echo "Tests successful: ${{ steps.test.outputs.success }}"
    echo "Total tests: ${{ steps.test.outputs.tests-total }}"
    echo "Passed: ${{ steps.test.outputs.tests-passed }}"
    echo "Failed: ${{ steps.test.outputs.tests-failed }}"
    echo "Skipped: ${{ steps.test.outputs.tests-skipped }}"
    
- name: Upload coverage report
  if: steps.test.outputs.coverage-report-path != ''
  uses: actions/upload-artifact@v4
  with:
    name: coverage-report
    path: ${{ steps.test.outputs.coverage-report-path }}
```

## Environment Requirements

- .NET SDK must be pre-installed (use `actions/setup-dotnet` before this action)
- Test projects must be built (unless `no-build` is `true`)
- Access to test dependencies

## Error Handling

The action will:
- Validate that test project files exist matching the specified pattern
- Parse test output to extract test statistics
- Provide detailed error messages for troubleshooting
- Set appropriate exit codes on test failure
- Output comprehensive test results

## Examples

### Run All Tests

```yaml
steps:
  - uses: actions/checkout@v4
  - uses: actions/setup-dotnet@v4
    with:
      dotnet-version: '8.0.x'
  - uses: ./.github/actions/dotnet-restore
    with:
      projects: '**/*.csproj'
  - uses: ./.github/actions/dotnet-build
    with:
      projects: '**/*.csproj'
      configuration: 'Release'
  - uses: ./.github/actions/dotnet-test
    with:
      projects: '**/*Tests/*.csproj'
      configuration: 'Release'
```

### Test with Coverage Collection

```yaml
- name: Test with coverage
  id: test
  uses: ./.github/actions/dotnet-test
  with:
    projects: '**/*Tests/*.csproj'
    configuration: 'Release'
    collect-coverage: 'true'

- name: Upload coverage reports
  uses: actions/upload-artifact@v4
  with:
    name: coverage-reports
    path: ${{ steps.test.outputs.coverage-report-path }}
```

### Test with Failure Threshold

```yaml
- name: Test
  id: test
  uses: ./.github/actions/dotnet-test
  with:
    projects: '**/*Tests/*.csproj'
    configuration: 'Release'

- name: Check test coverage
  run: |
    total=${{ steps.test.outputs.tests-total }}
    passed=${{ steps.test.outputs.tests-passed }}
    if [ $total -gt 0 ]; then
      coverage=$((passed * 100 / total))
      echo "Test coverage: ${coverage}%"
      if [ $coverage -lt 80 ]; then
        echo "Test coverage below 80%"
        exit 1
      fi
    fi
```

## Troubleshooting

### Common Issues

1. **No test projects found**: Verify the `projects` glob pattern matches your test project structure
2. **Test failures**: Check the test output for specific test failure details
3. **Missing test dependencies**: Ensure all test dependencies are restored and built
4. **Coverage collection issues**: Verify coverage tools are available in the test environment

### Debug Mode

For troubleshooting, use higher verbosity:

```yaml
- uses: ./.github/actions/dotnet-test
  with:
    projects: '**/*Tests/*.csproj'
    configuration: 'Debug'
    verbosity: 'diagnostic'
```

## License

This action is provided as part of the GitHub Actions Migration Agent toolkit.