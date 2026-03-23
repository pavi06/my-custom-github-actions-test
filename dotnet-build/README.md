# dotnet-build Action

A GitHub Action to build .NET projects.

## Description

This action executes `dotnet build` command with specified configuration and parameters to build .NET projects. It supports glob patterns for project selection and provides detailed output about the build operation including warnings and errors count.

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------||
| `projects` | Glob pattern for project files (e.g., `**/*.csproj`) | Yes | - |
| `configuration` | Build configuration (e.g., `Release`, `Debug`) | Yes | - |
| `working-directory` | Working directory for the operation | No | `.` |
| `verbosity` | Verbosity level (`quiet`, `minimal`, `normal`, `detailed`, `diagnostic`) | No | `minimal` |
| `no-restore` | Skip restore during build | No | `false` |
| `output-directory` | Output directory for build artifacts | No | - |

## Outputs

| Output | Description |
|--------|-------------|
| `success` | Boolean indicating if build was successful |
| `build-output-path` | Path to build output directory |
| `warnings-count` | Number of build warnings |
| `errors-count` | Number of build errors |

## Usage

### Basic Usage

```yaml
- name: Build
  uses: ./.github/actions/dotnet-build
  with:
    projects: '**/*.csproj'
    configuration: 'Release'
```

### Advanced Usage

```yaml
- name: Build with custom settings
  uses: ./.github/actions/dotnet-build
  with:
    projects: 'src/**/*.csproj'
    configuration: 'Debug'
    working-directory: './MyProject'
    verbosity: 'normal'
    no-restore: 'true'
    output-directory: './build-output'
```

### Using Outputs

```yaml
- name: Build
  id: build
  uses: ./.github/actions/dotnet-build
  with:
    projects: '**/*.csproj'
    configuration: 'Release'

- name: Check build results
  run: |
    echo "Build successful: ${{ steps.build.outputs.success }}"
    echo "Build output path: ${{ steps.build.outputs.build-output-path }}"
    echo "Warnings: ${{ steps.build.outputs.warnings-count }}"
    echo "Errors: ${{ steps.build.outputs.errors-count }}"
```

## Environment Requirements

- .NET SDK must be pre-installed (use `actions/setup-dotnet` before this action)
- Dependencies must be restored (unless `no-restore` is `true`)
- Write access to output directories

## Error Handling

The action will:
- Validate that project files exist matching the specified pattern
- Parse build output to count warnings and errors
- Provide detailed error messages for troubleshooting
- Set appropriate exit codes on failure
- Output build statistics and paths

## Examples

### Build All Projects in Release Mode

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
```

### Build with Custom Output Directory

```yaml
steps:
  - uses: actions/checkout@v4
  - uses: actions/setup-dotnet@v4
    with:
      dotnet-version: '8.0.x'
  - uses: ./.github/actions/dotnet-build
    with:
      projects: 'src/MyApp/*.csproj'
      configuration: 'Release'
      output-directory: './artifacts/build'
      no-restore: 'true'
```

### Build with Warning Threshold

```yaml
- name: Build
  id: build
  uses: ./.github/actions/dotnet-build
  with:
    projects: '**/*.csproj'
    configuration: 'Release'

- name: Check for warnings
  run: |
    if [ "${{ steps.build.outputs.warnings-count }}" -gt "10" ]; then
      echo "Too many warnings: ${{ steps.build.outputs.warnings-count }}"
      exit 1
    fi
```

## Troubleshooting

### Common Issues

1. **No project files found**: Verify the `projects` glob pattern matches your repository structure
2. **Build errors**: Check the build output for specific compilation errors
3. **Missing dependencies**: Ensure restore was run before build (unless `no-restore` is `true`)
4. **Permission errors**: Ensure the runner has write access to output directories

### Debug Mode

For troubleshooting, use higher verbosity:

```yaml
- uses: ./.github/actions/dotnet-build
  with:
    projects: '**/*.csproj'
    configuration: 'Debug'
    verbosity: 'diagnostic'
```

## License

This action is provided as part of the GitHub Actions Migration Agent toolkit.