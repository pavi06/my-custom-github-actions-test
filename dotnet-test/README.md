# .NET Test Action

A GitHub Action to execute .NET tests using VSTest/dotnet test.

## Description

This action executes `dotnet test` command to run .NET tests with specified configuration and platform settings. It provides comprehensive test execution with result parsing, parallel execution support, and optional code coverage collection.

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `configuration` | Build configuration | Yes | - |
| `platform` | Target platform | Yes | - |
| `test-assembly-pattern` | Pattern for test assemblies | Yes | - |
| `exclude-pattern` | Exclusion pattern for test files | No | `''` |
| `results-folder` | Test results output folder | No | `TestResults` |
| `run-in-parallel` | Run tests in parallel | No | `false` |
| `code-coverage` | Enable code coverage | No | `false` |

## Outputs

| Output | Description |
|--------|-------------|
| `test-results` | Test execution results |
| `tests-passed` | Number of tests passed |
| `tests-failed` | Number of tests failed |

## Usage

### Basic Usage

```yaml
- name: Run tests
  uses: pavi06/my-custom-github-actions-test/dotnet-test@main
  with:
    configuration: 'Release'
    platform: 'Any CPU'
    test-assembly-pattern: '**/*test*.dll'
```

### Advanced Usage

```yaml
- name: Run tests with coverage
  uses: pavi06/my-custom-github-actions-test/dotnet-test@main
  with:
    configuration: 'Debug'
    platform: 'x64'
    test-assembly-pattern: '**/*Tests.dll'
    exclude-pattern: '!**/obj/**'
    results-folder: 'CustomTestResults'
    run-in-parallel: 'true'
    code-coverage: 'true'
```

### Using Outputs

```yaml
- name: Run tests
  id: test
  uses: pavi06/my-custom-github-actions-test/dotnet-test@main
  with:
    configuration: 'Release'
    platform: 'Any CPU'
    test-assembly-pattern: '**/*test*.dll'

- name: Check test results
  run: |
    echo "Test results: ${{ steps.test.outputs.test-results }}"
    echo "Tests passed: ${{ steps.test.outputs.tests-passed }}"
    echo "Tests failed: ${{ steps.test.outputs.tests-failed }}"
```

## Environment Requirements

- .NET SDK must be pre-installed (use `actions/setup-dotnet` before this action)
- Test assemblies must be built before running tests
- Access to test dependencies and test data

## Error Handling

The action will:
- Search for test assemblies matching the specified pattern
- Apply exclusion patterns to filter out unwanted files
- Execute tests with proper error handling
- Parse test results from TRX files
- Set appropriate exit codes on test failure
- Provide detailed test execution statistics

## Examples

### Complete Test Pipeline

```yaml
steps:
  - uses: actions/checkout@v4
  
  - name: Setup .NET
    uses: actions/setup-dotnet@v4
    with:
      dotnet-version: '6.0.x'
      
  - name: Restore packages
    uses: pavi06/my-custom-github-actions-test/nuget-restore@main
    with:
      solution: '**/*.sln'
      
  - name: Build solution
    uses: pavi06/my-custom-github-actions-test/dotnet-build@main
    with:
      solution: '**/*.sln'
      configuration: 'Release'
      platform: 'Any CPU'
      
  - name: Run tests
    uses: pavi06/my-custom-github-actions-test/dotnet-test@main
    with:
      configuration: 'Release'
      platform: 'Any CPU'
      test-assembly-pattern: '**/*test*.dll'
      exclude-pattern: '!**/obj/**'
```

### Test with Coverage Collection

```yaml
- name: Run tests with coverage
  id: test
  uses: pavi06/my-custom-github-actions-test/dotnet-test@main
  with:
    configuration: 'Release'
    platform: 'Any CPU'
    test-assembly-pattern: '**/*Tests.dll'
    code-coverage: 'true'

- name: Upload test results
  uses: actions/upload-artifact@v4
  if: always()
  with:
    name: test-results
    path: TestResults/
```

### Parallel Test Execution

```yaml
- name: Run tests in parallel
  uses: pavi06/my-custom-github-actions-test/dotnet-test@main
  with:
    configuration: 'Release'
    platform: 'Any CPU'
    test-assembly-pattern: '**/*test*.dll'
    run-in-parallel: 'true'
    results-folder: 'ParallelTestResults'
```

### Test with Failure Handling

```yaml
- name: Run tests
  id: test
  uses: pavi06/my-custom-github-actions-test/dotnet-test@main
  with:
    configuration: 'Release'
    platform: 'Any CPU'
    test-assembly-pattern: '**/*test*.dll'
  continue-on-error: true

- name: Report test results
  run: |
    if [ "${{ steps.test.outputs.test-results }}" = "failure" ]; then
      echo "Tests failed: ${{ steps.test.outputs.tests-failed }} failures"
      echo "Tests passed: ${{ steps.test.outputs.tests-passed }}"
    else
      echo "All tests passed: ${{ steps.test.outputs.tests-passed }}"
    fi
```

## Test Assembly Pattern Examples

| Pattern | Description |
|---------|-------------|
| `**/*test*.dll` | All DLLs containing 'test' in the name |
| `**/*Tests.dll` | All DLLs ending with 'Tests.dll' |
| `**/bin/Release/*test*.dll` | Test DLLs in Release build folders |
| `tests/**/*.dll` | All DLLs in tests directory |

## Exclusion Pattern Examples

| Pattern | Description |
|---------|-------------|
| `!**/obj/**` | Exclude files in obj directories |
| `!**/bin/Debug/**` | Exclude Debug build outputs |
| `!**/*Integration*` | Exclude integration test assemblies |

## Troubleshooting

### Common Issues

1. **No test assemblies found**: Verify the `test-assembly-pattern` matches your build output structure
2. **Test failures**: Check the test output and TRX files for specific test failure details
3. **Missing test dependencies**: Ensure all test dependencies are available at runtime
4. **Coverage collection issues**: Verify coverage tools are installed and accessible

### Debug Mode

For troubleshooting, check the action logs for:
- Test assembly discovery process
- Find command execution
- Test execution output
- TRX file parsing results
- Test statistics calculation

### No Tests Found

If no tests are found:
1. Verify your test assemblies are built in the specified configuration
2. Check that test assembly names match the pattern
3. Ensure test assemblies are not excluded by the exclusion pattern
4. Verify the build output directory structure

## Integration with Other Actions

This action works well with:
- `actions/setup-dotnet` - Set up .NET SDK
- `pavi06/my-custom-github-actions-test/dotnet-build@main` - Build before testing
- `actions/upload-artifact@v4` - Upload test results and coverage reports
- Test reporting actions for processing TRX files

## License

This action is provided as part of the GitHub Actions Migration Agent toolkit.