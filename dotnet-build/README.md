# .NET Build Action

A GitHub Action to build .NET solutions using MSBuild/dotnet build.

## Description

This action executes `dotnet build` command to build .NET solutions with specified configuration and platform settings. It provides comprehensive build management including optional cleaning and package restoration.

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `solution` | Path to solution file | Yes | - |
| `configuration` | Build configuration (Debug/Release) | Yes | - |
| `platform` | Target platform | Yes | - |
| `vs-version` | Visual Studio version | No | `latest` |
| `clean` | Clean before build | No | `false` |
| `restore-packages` | Restore NuGet packages before build | No | `false` |

## Outputs

| Output | Description |
|--------|-------------|
| `build-status` | Success/Failure status |
| `build-output-path` | Path to build outputs |

## Usage

### Basic Usage

```yaml
- name: Build solution
  uses: pavi06/my-custom-github-actions-test/dotnet-build@main
  with:
    solution: '**/*.sln'
    configuration: 'Release'
    platform: 'Any CPU'
```

### Advanced Usage

```yaml
- name: Build with clean and restore
  uses: pavi06/my-custom-github-actions-test/dotnet-build@main
  with:
    solution: 'src/MyApp.sln'
    configuration: 'Debug'
    platform: 'x64'
    clean: 'true'
    restore-packages: 'true'
    vs-version: '2022'
```

### Using Outputs

```yaml
- name: Build solution
  id: build
  uses: pavi06/my-custom-github-actions-test/dotnet-build@main
  with:
    solution: '**/*.sln'
    configuration: 'Release'
    platform: 'Any CPU'

- name: Check build results
  run: |
    echo "Build status: ${{ steps.build.outputs.build-status }}"
    echo "Build output path: ${{ steps.build.outputs.build-output-path }}"
```

## Environment Requirements

- .NET SDK must be pre-installed (use `actions/setup-dotnet` before this action)
- Solution file must exist at the specified path
- Write access to build output directories

## Error Handling

The action will:
- Validate that the solution file exists
- Optionally clean the solution before building
- Optionally restore NuGet packages before building
- Execute the build with proper error handling
- Set appropriate exit codes on failure
- Provide detailed build status information

## Examples

### Complete Build Pipeline

```yaml
steps:
  - uses: actions/checkout@v4
  
  - name: Setup .NET
    uses: actions/setup-dotnet@v4
    with:
      dotnet-version: '6.0.x'
      
  - name: Restore NuGet packages
    uses: pavi06/my-custom-github-actions-test/nuget-restore@main
    with:
      solution: '**/*.sln'
      verbosity: 'normal'
      
  - name: Build solution
    uses: pavi06/my-custom-github-actions-test/dotnet-build@main
    with:
      solution: '**/*.sln'
      configuration: 'Release'
      platform: 'Any CPU'
```

### Build with Clean

```yaml
- name: Clean and build
  uses: pavi06/my-custom-github-actions-test/dotnet-build@main
  with:
    solution: 'MyProject.sln'
    configuration: 'Release'
    platform: 'Any CPU'
    clean: 'true'
```

### Build Multiple Configurations

```yaml
strategy:
  matrix:
    configuration: [Debug, Release]
    platform: [x86, x64, 'Any CPU']

steps:
  - uses: actions/checkout@v4
  
  - name: Setup .NET
    uses: actions/setup-dotnet@v4
    with:
      dotnet-version: '6.0.x'
      
  - name: Build ${{ matrix.configuration }} - ${{ matrix.platform }}
    uses: pavi06/my-custom-github-actions-test/dotnet-build@main
    with:
      solution: '**/*.sln'
      configuration: ${{ matrix.configuration }}
      platform: ${{ matrix.platform }}
```

## Troubleshooting

### Common Issues

1. **Solution file not found**: Verify the `solution` path pattern matches your repository structure
2. **Build errors**: Check the build output for specific compilation errors
3. **Missing dependencies**: Ensure NuGet packages are restored before building
4. **Platform mismatch**: Verify the specified platform is supported by your solution

### Debug Mode

For troubleshooting, the action provides detailed logging by default. Check the action logs for:
- Solution file discovery
- Clean operation status
- Package restoration status
- Build command execution
- Build output and errors

## Integration with Other Actions

This action works well with:
- `actions/setup-dotnet` - Set up .NET SDK
- `pavi06/my-custom-github-actions-test/nuget-restore@main` - Restore packages
- `pavi06/my-custom-github-actions-test/dotnet-test@main` - Run tests
- `pavi06/my-custom-github-actions-test/copy-artifacts@main` - Copy build outputs

## License

This action is provided as part of the GitHub Actions Migration Agent toolkit.