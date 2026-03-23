# dotnet-publish Action

A GitHub Action to publish .NET applications for deployment.

## Description

This action executes `dotnet publish` command with specified parameters to publish .NET applications for deployment. It supports automatic web project detection, custom project selection, and various deployment configurations including self-contained deployments.

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------||
| `configuration` | Publish configuration (e.g., `Release`, `Debug`) | Yes | - |
| `output-path` | Output directory for published files | Yes | - |
| `working-directory` | Working directory for the operation | No | `.` |
| `publish-web-projects` | Automatically detect and publish web projects | No | `false` |
| `projects` | Specific projects to publish (if not using auto-detection) | No | - |
| `runtime` | Target runtime identifier (e.g., `win-x64`, `linux-x64`) | No | - |
| `self-contained` | Create self-contained deployment | No | `false` |
| `verbosity` | Verbosity level (`quiet`, `minimal`, `normal`, `detailed`, `diagnostic`) | No | `minimal` |

## Outputs

| Output | Description |
|--------|-------------|
| `success` | Boolean indicating if publish was successful |
| `publish-path` | Path to published output |
| `published-files-count` | Number of files in publish output |
| `publish-size` | Total size of published output in bytes |

## Usage

### Basic Usage - Auto-detect Web Projects

```yaml
- name: Publish
  uses: ./.github/actions/dotnet-publish
  with:
    configuration: 'Release'
    output-path: './artifacts'
    publish-web-projects: 'true'
```

### Publish Specific Projects

```yaml
- name: Publish specific projects
  uses: ./.github/actions/dotnet-publish
  with:
    configuration: 'Release'
    output-path: './publish'
    projects: 'src/MyApp/*.csproj'
```

### Self-Contained Deployment

```yaml
- name: Publish self-contained
  uses: ./.github/actions/dotnet-publish
  with:
    configuration: 'Release'
    output-path: './deploy'
    publish-web-projects: 'true'
    runtime: 'linux-x64'
    self-contained: 'true'
```

### Using Outputs

```yaml
- name: Publish
  id: publish
  uses: ./.github/actions/dotnet-publish
  with:
    configuration: 'Release'
    output-path: './artifacts'
    publish-web-projects: 'true'

- name: Check publish results
  run: |
    echo "Publish successful: ${{ steps.publish.outputs.success }}"
    echo "Publish path: ${{ steps.publish.outputs.publish-path }}"
    echo "Files count: ${{ steps.publish.outputs.published-files-count }}"
    echo "Total size: ${{ steps.publish.outputs.publish-size }} bytes"

- name: Upload artifacts
  uses: actions/upload-artifact@v4
  with:
    name: published-app
    path: ${{ steps.publish.outputs.publish-path }}
```

## Environment Requirements

- .NET SDK must be pre-installed (use `actions/setup-dotnet` before this action)
- Projects must be built (unless using `--no-build` option)
- Write access to output directory
- Sufficient disk space for published output

## Web Project Auto-Detection

When `publish-web-projects` is set to `true`, the action automatically detects web projects by looking for:
- Projects using `Microsoft.NET.Sdk.Web` SDK
- Projects with `Microsoft.AspNetCore` package references
- Projects with `Microsoft.AspNetCore.App` framework references

## Error Handling

The action will:
- Validate that projects exist matching the specified criteria
- Create output directories if they don't exist
- Handle multiple project publishing with individual success tracking
- Provide detailed file count and size information
- Set appropriate exit codes on failure

## Examples

### Complete CI/CD Pipeline

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
  
  - uses: ./.github/actions/dotnet-publish
    with:
      configuration: 'Release'
      output-path: './artifacts'
      publish-web-projects: 'true'
  
  - uses: actions/upload-artifact@v4
    with:
      name: published-application
      path: ./artifacts
```

### Multi-Runtime Publishing

```yaml
strategy:
  matrix:
    runtime: ['win-x64', 'linux-x64', 'osx-x64']

steps:
  - uses: actions/checkout@v4
  - uses: actions/setup-dotnet@v4
    with:
      dotnet-version: '8.0.x'
  
  - name: Publish for ${{ matrix.runtime }}
    uses: ./.github/actions/dotnet-publish
    with:
      configuration: 'Release'
      output-path: './artifacts/${{ matrix.runtime }}'
      publish-web-projects: 'true'
      runtime: ${{ matrix.runtime }}
      self-contained: 'true'
```

### Conditional Publishing

```yaml
- name: Publish web projects
  if: contains(github.event.head_commit.message, '[deploy]')
  uses: ./.github/actions/dotnet-publish
  with:
    configuration: 'Release'
    output-path: './deploy'
    publish-web-projects: 'true'
```

## Troubleshooting

### Common Issues

1. **No projects found**: Verify project patterns or enable web project auto-detection
2. **Output directory permissions**: Ensure the runner has write access to the output path
3. **Insufficient disk space**: Check available space for large self-contained deployments
4. **Runtime not supported**: Verify the target runtime is supported by your .NET version

### Debug Mode

For troubleshooting, use higher verbosity:

```yaml
- uses: ./.github/actions/dotnet-publish
  with:
    configuration: 'Release'
    output-path: './artifacts'
    publish-web-projects: 'true'
    verbosity: 'diagnostic'
```

## License

This action is provided as part of the GitHub Actions Migration Agent toolkit.