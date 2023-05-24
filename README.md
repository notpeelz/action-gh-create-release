# GitHub Create Release

A GitHub action to automate the creation of releases.

Source code available at https://github.com/notpeelz/github-create-release

## Usage

```yaml
on:
  workflow_dispatch:
    inputs:
      version:
        description: "Version"
        required: true
        type: string

jobs:
  publish-release:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout branch
        uses: actions/checkout@v3
      - uses: notpeelz/gh-create-release@v1.0.1
        with:
          token: ${{ github.token }}
          tag: v${{ inputs.version }}
          title: v${{ inputs.version }}
          files: |
            file1.txt
            file2.txt
```

## Parameters

| Parameter | Description | Default |
|-----------|-------------|---------|
| `token`[^required] | GitHub access token |  |
| `ref` | The git ref used to create the tag and release.<br/>If not specified, defaults to the commit SHA that triggered the workflow.<br/> |  |
| `tag`[^required] | The name of the tag associated with the release.<br/> |  |
| `tagMessage` | The message associated with the tag (defaults to the name of the tag)<br/> |  |
| `strategy` | Determines what should be done if the tag already exists.<br/>Possible values:<br/>  `failFast` - if the tag already exists, aborts the release creation<br/>  `useExisting` - uses an existing tag (`ref` parameter is ignored)<br/>  `skip` - if the tag already exists, do nothing<br/>  `replace` - replaces the tag along with associated releases<br/> | `failFast` |
| `title` | The title of the release.<br/> |  |
| `title_source` | Determines where the the title should be read from.<br/>Possible values:<br/>  `literal` - uses the "title" parameter as-is<br/>  `file` - reads from the file specified in the "title" parameter<br/>  `env` - reads from the environment variable specified in the "title" parameter<br/> | `literal` |
| `body` | The message associated with the release.<br/> |  |
| `body_source` | Determines where the the body should be read from.<br/>Possible values:<br/>  `literal` - uses the "body" parameter as-is<br/>  `file` - reads from the file specified in the "body" parameter<br/>  `env` - reads from the environment variable specified in the "body" parameter<br/> | `literal` |
| `prerelease` | If true, the release will be marked as a pre-release.<br/> |  |
| `draft` | If true, the release will be made into a draft. |  |
| `discussion_category_name` | If specified, a discussion of the specified category is created and<br/>linked to the release.<br/> |  |
| `files` | Newline-separated list of files to upload (supports globbing).<br/>Missing files are ignored.<br/> |  |

[^required]: required

## Outputs

| Parameter | Description |
|-----------|-------------|
| `release_id` | The identifier of the release that was created.<br/> |
