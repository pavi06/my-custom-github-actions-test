# dotnet-restore Action

A GitHub Action to restore NuGet packages for .NET projects.

## Description

This action executes `dotnet restore` command with specified parameters to restore NuGet packages for .NET projects. It supports glob patterns for project selection and provides detailed output about the restore operation.

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------||
| `projects` | Glob pattern for project files (e.g., `**/*.csproj`) | Yes | - |
| `working-directory` | Working directory for the operation | No | `.` |
| `verbosity` | Verbosity level (`quiet`, `minimal`, `normal`, `detailed`, `diagnostic`) | No | `minimal` |
| `no-cache` | Skip using the cache | No | `false` |

## Outputs

| Output | Description |
|--------|-------------|
| `success` | Boolean indicating if restore was successful |
| `packages-restored` | Number of packages restored |

## Usage

### Basic Usage

```yaml
- name: Restore dependencies
  uses: ./.github/actions/dotnet-restore
  with:
    projects: '**/*.csproj'
```

### Advanced Usage

```yaml
- name: Restore dependencies with custom settings
  uses: ./.github/actions/dotnet-restore
  with:
    projects: 'src/**/*.csproj'
    working-directory: './MyProject'
    verbosity: 'normal'
    no-cache: 'true'
```

### Using Outputs

```yaml
- name: Restore dependencies
  id: restore
  uses: ./.github/actions/dotnet-restore
  with:
    projects: '**/*.csproj'

- name: Check restore results
  run: |
    echo "Restore successful: ${{ steps.restore.outputs.success }}"
    echo "Packages restored: ${{ steps.restore.outputs.packages-restored }}"
```

## Environment Requirements

- .NET SDK must be pre-installed (use `actions/setup-dotnet` before this action)
- Access to NuGet package sources
- Read access to project files
- Internet connectivity for package downloads

## Error Handling

The action will:
- Validate that project files exist matching the specified pattern
- Provide detailed error messages for troubleshooting
- Set appropriate exit codes on failure
- Output success status and package count information

## Examples

### Restore All Projects

```yaml
steps:
  - uses: actions/checkout@v4
  - uses: actions/setup-dotnet@v4
    with:
      dotnet-version: '8.0.x'
  - uses: ./.github/actions/dotnet-restore
    with:
      projects: '**/*.csproj'
```

### Restore Specific Projects

```yaml
steps:
  - uses: actions/checkout@v4
  - uses: actions/setup-dotnet@v4
    with:
      dotnet-version: '8.0.x'
  - uses: ./.github/actions/dotnet-restore
    with:
      projects: 'src/MyApp/*.csproj'
      verbosity: 'detailed'
```

## Troubleshooting

### Common Issues

1. **No project files found**: Verify the `projects` glob pattern matches your repository structure
2. **Permission errors**: Ensure the runner has read access to project files and write access for package cache
3. **Network issues**: Check internet connectivity and NuGet source accessibility
4. **Missing .NET SDK**: Ensure `actions/setup-dotnet` is run before this action

### Debug Mode

For troubleshooting, use higher verbosity:

```yaml
- uses: ./.github/actions/dotnet-restore
  with:
    projects: '**/*.csproj'
    verbosity: 'diagnostic'
```

## License

This action is provided as part of the GitHub Actions Migration Agent toolkit.