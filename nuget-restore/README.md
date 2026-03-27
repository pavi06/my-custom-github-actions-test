# NuGet Restore Action

A GitHub Action to restore NuGet packages for .NET solutions.

## Description

This action executes `nuget restore` command to restore NuGet packages for .NET solutions and projects. It provides comprehensive package restoration with configurable verbosity, caching options, and parallel processing control.

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `solution` | Path to solution file or packages.config | Yes | - |
| `verbosity` | Verbosity level for restore operation | No | `normal` |
| `no-cache` | Disable package caching | No | `false` |
| `disable-parallel` | Disable parallel processing | No | `false` |

## Outputs

| Output | Description |
|--------|-------------|
| `packages-restored` | Number of packages restored |

## Usage

### Basic Usage

```yaml
- name: Restore NuGet packages
  uses: pavi06/my-custom-github-actions-test/nuget-restore@main
  with:
    solution: '**/*.sln'
```

### Advanced Usage

```yaml
- name: Restore with custom settings
  uses: pavi06/my-custom-github-actions-test/nuget-restore@main
  with:
    solution: 'src/MyApp.sln'
    verbosity: 'Detailed'
    no-cache: 'true'
    disable-parallel: 'true'
```

### Using Outputs

```yaml
- name: Restore packages
  id: restore
  uses: pavi06/my-custom-github-actions-test/nuget-restore@main
  with:
    solution: '**/*.sln'
    verbosity: 'normal'

- name: Check restore results
  run: |
    echo "Packages restored: ${{ steps.restore.outputs.packages-restored }}"
```

## Verbosity Levels

| Level | Description |
|-------|-------------|
| `quiet` | Minimal output |
| `normal` | Standard output (default) |
| `detailed` | Detailed operation information |
| `diagnostic` | Maximum diagnostic information |

## Features

### Package Management
- Restores packages for solution files (.sln)
- Supports packages.config files
- Handles package dependencies automatically

### Performance Options
- **Parallel processing**: Enable/disable parallel package downloads
- **Package caching**: Control local package cache usage
- **Verbosity control**: Adjust output detail level

### Error Handling
- Comprehensive error reporting
- Package count tracking
- Exit code management

## Examples

### Basic Package Restore

```yaml
steps:
  - uses: actions/checkout@v4
  
  - name: Setup NuGet
    uses: nuget/setup-nuget@v2
    with:
      nuget-version: '4.4.1'
      
  - name: Restore packages
    uses: pavi06/my-custom-github-actions-test/nuget-restore@main
    with:
      solution: '**/*.sln'
```

### Detailed Restore with Diagnostics

```yaml
- name: Restore with detailed logging
  uses: pavi06/my-custom-github-actions-test/nuget-restore@main
  with:
    solution: 'MyProject.sln'
    verbosity: 'Detailed'
```

### Fast Restore (No Cache)

```yaml
- name: Fresh package restore
  uses: pavi06/my-custom-github-actions-test/nuget-restore@main
  with:
    solution: '**/*.sln'
    no-cache: 'true'
    verbosity: 'minimal'
```

### Sequential Restore

```yaml
- name: Sequential package restore
  uses: pavi06/my-custom-github-actions-test/nuget-restore@main
  with:
    solution: '**/*.sln'
    disable-parallel: 'true'
    verbosity: 'normal'
```

### Complete Build Pipeline

```yaml
steps:
  - uses: actions/checkout@v4
  
  - name: Setup .NET
    uses: actions/setup-dotnet@v4
    with:
      dotnet-version: '6.0.x'
      
  - name: Setup NuGet
    uses: nuget/setup-nuget@v2
    with:
      nuget-version: '4.4.1'
      
  - name: Restore NuGet packages
    id: restore
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
      
  - name: Report restore statistics
    run: |
      echo "Packages restored: ${{ steps.restore.outputs.packages-restored }}"
```

## Environment Requirements

- NuGet CLI must be pre-installed (use `nuget/setup-nuget` before this action)
- Solution or packages.config file must exist at the specified path
- Internet connectivity for package downloads (unless using local feeds)
- Write access to packages directory

## Error Handling

The action will:
- Validate that the solution/packages.config file exists
- Execute NuGet restore with specified parameters
- Parse output to count restored packages
- Provide detailed error messages on failure
- Set appropriate exit codes for build pipeline integration

## Troubleshooting

### Common Issues

1. **Solution file not found**: Verify the `solution` path pattern matches your repository structure
2. **Package restore failures**: Check network connectivity and package source availability
3. **Authentication issues**: Ensure proper credentials for private package feeds
4. **Disk space**: Verify sufficient space for package downloads

### Debug Mode

For troubleshooting, use detailed verbosity:

```yaml
- name: Debug restore
  uses: pavi06/my-custom-github-actions-test/nuget-restore@main
  with:
    solution: '**/*.sln'
    verbosity: 'diagnostic'
```

### Performance Optimization

For faster restores:
- Use package caching (default behavior)
- Enable parallel processing (default behavior)
- Use appropriate verbosity level (normal or minimal)

For reliability:
- Disable parallel processing if experiencing network issues
- Use no-cache for clean package restoration
- Use detailed verbosity for troubleshooting

## Integration with Other Actions

This action works well with:
- `nuget/setup-nuget` - Set up NuGet CLI
- `actions/setup-dotnet` - Set up .NET SDK
- `pavi06/my-custom-github-actions-test/dotnet-build@main` - Build after restore
- Package caching actions for improved performance

## License

This action is provided as part of the GitHub Actions Migration Agent toolkit.