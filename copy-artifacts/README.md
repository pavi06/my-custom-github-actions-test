# Copy Artifacts Action

A GitHub Action to copy files and folders with pattern matching.

## Description

This action provides flexible file copying capabilities with support for glob patterns, folder structure preservation or flattening, and comprehensive copy operations for build artifacts and other files.

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `source-folder` | Source directory path | Yes | - |
| `contents` | File patterns to copy | Yes | - |
| `target-folder` | Destination directory | Yes | - |
| `clean-target` | Clean target folder before copy | No | `false` |
| `overwrite` | Overwrite existing files | No | `false` |
| `flatten-folders` | Flatten folder structure | No | `false` |

## Outputs

| Output | Description |
|--------|-------------|
| `files-copied` | Number of files copied |
| `copy-status` | Success/Failure status |

## Usage

### Basic Usage

```yaml
- name: Copy build artifacts
  uses: pavi06/my-custom-github-actions-test/copy-artifacts@main
  with:
    source-folder: ${{ github.workspace }}
    contents: '**/bin/Release/**'
    target-folder: ${{ github.workspace }}/artifacts
```

### Advanced Usage

```yaml
- name: Copy with options
  uses: pavi06/my-custom-github-actions-test/copy-artifacts@main
  with:
    source-folder: './build'
    contents: |
      **/*.dll
      **/*.exe
      **/*.config
    target-folder: './deployment'
    clean-target: 'true'
    overwrite: 'true'
    flatten-folders: 'false'
```

### Using Outputs

```yaml
- name: Copy artifacts
  id: copy
  uses: pavi06/my-custom-github-actions-test/copy-artifacts@main
  with:
    source-folder: ${{ github.workspace }}
    contents: '**/bin/Release/**'
    target-folder: './artifacts'

- name: Check copy results
  run: |
    echo "Copy status: ${{ steps.copy.outputs.copy-status }}"
    echo "Files copied: ${{ steps.copy.outputs.files-copied }}"
```

## File Pattern Examples

| Pattern | Description |
|---------|-------------|
| `**/*.dll` | All DLL files recursively |
| `**/bin/Release/**` | All files in Release build folders |
| `**/*.{exe,dll,config}` | Executable, library, and config files |
| `src/**/*.cs` | All C# source files in src directory |

## Features

### Pattern Matching
- Supports glob patterns for flexible file selection
- Multiple patterns can be specified (space or newline separated)
- Recursive directory traversal

### Folder Structure Options
- **Preserve structure** (default): Maintains original directory hierarchy
- **Flatten folders**: Copies all files to target root, ignoring subdirectories

### Copy Behavior
- **Clean target**: Optionally remove target directory before copying
- **Overwrite control**: Choose whether to overwrite existing files
- **Detailed logging**: Comprehensive output of copy operations

## Examples

### Copy Build Outputs

```yaml
- name: Copy build artifacts
  uses: pavi06/my-custom-github-actions-test/copy-artifacts@main
  with:
    source-folder: ${{ github.workspace }}
    contents: '**/bin/${{ matrix.configuration }}/**'
    target-folder: ${{ github.workspace }}/artifacts
    clean-target: 'true'
```

### Copy Multiple File Types

```yaml
- name: Copy deployment files
  uses: pavi06/my-custom-github-actions-test/copy-artifacts@main
  with:
    source-folder: './src'
    contents: |
      **/*.dll
      **/*.exe
      **/*.json
      **/*.xml
    target-folder: './deploy'
    overwrite: 'true'
```

### Flatten Directory Structure

```yaml
- name: Copy and flatten
  uses: pavi06/my-custom-github-actions-test/copy-artifacts@main
  with:
    source-folder: './build'
    contents: '**/*.log'
    target-folder: './logs'
    flatten-folders: 'true'
    clean-target: 'true'
```

### Copy with Upload

```yaml
- name: Copy build artifacts
  id: copy
  uses: pavi06/my-custom-github-actions-test/copy-artifacts@main
  with:
    source-folder: ${{ github.workspace }}
    contents: '**/bin/Release/**'
    target-folder: './artifacts'

- name: Upload artifacts
  uses: actions/upload-artifact@v4
  if: steps.copy.outputs.copy-status == 'success'
  with:
    name: build-artifacts
    path: ./artifacts
    retention-days: 30
```

## Environment Requirements

- Source directory must exist and be accessible
- Write permissions to target directory
- Sufficient disk space for copied files

## Error Handling

The action will:
- Validate source and target directories
- Create target directories as needed
- Handle file permission issues gracefully
- Provide detailed error messages
- Set appropriate exit codes on failure
- Count and report copied files

## Troubleshooting

### Common Issues

1. **No files found**: Verify the `contents` pattern matches existing files in the source folder
2. **Permission denied**: Ensure the action has write access to the target directory
3. **Disk space**: Check available disk space for large copy operations
4. **Pattern syntax**: Verify glob patterns are correctly formatted

### Debug Information

The action provides detailed logging including:
- Source and target directory paths
- File patterns being processed
- Individual file copy operations
- Final copy statistics

## Integration with Other Actions

This action works well with:
- `pavi06/my-custom-github-actions-test/dotnet-build@main` - Copy build outputs
- `actions/upload-artifact@v4` - Upload copied files
- `actions/download-artifact@v4` - Download files for copying
- Deployment actions that need organized file structures

## License

This action is provided as part of the GitHub Actions Migration Agent toolkit.