# GitHub Create Release

A GitHub action to automate the creation of releases.

Source code available at https://github.com/notpeelz/github-create-release

## Parameters

| Parameter | Description | Default |
|-----------|-------------|---------|
| `token` | The GitHub access token.<br/> | `${{ github.token }}` |
| `repository` | The repository where the release should be created.<br/>For example, `octocat/hello-world`<br/> | `${{ github.repository }}` |
| `target` | The git object to tag. This can be a SHA or a ref to a git object.<br/> |  |
| `tag`[^required] | The name of the tag associated with the release.<br/> |  |
| `tag-message` | The message associated with the tag (defaults to the name of the tag)<br/> |  |
| `strategy` | Determines what should be done if the tag already exists.<br/>Possible values:<br/>  `fail-fast` - aborts with an error if the tag already exists<br/>  `use-existing-tag` - uses an existing tag, replacing associated releases (`target` parameter is ignored)<br/>  `replace` - replaces the tag along with associated releases<br/> | `fail-fast` |
| `title` | The title of the release.<br/> |  |
| `title-source` | Determines where the the title should be read from.<br/>Possible values:<br/>  `literal` - uses the "title" parameter as-is<br/>  `file` - reads from the file specified in the "title" parameter<br/>  `env` - reads from the environment variable specified in the "title" parameter<br/> | `literal` |
| `body` | The message associated with the release.<br/> |  |
| `body-source` | Determines where the the body should be read from.<br/>Possible values:<br/>  `literal` - uses the "body" parameter as-is<br/>  `file` - reads from the file specified in the "body" parameter<br/>  `env` - reads from the environment variable specified in the "body" parameter<br/> | `literal` |
| `prerelease` | If true, the release will be marked as a pre-release.<br/> |  |
| `draft` | If true, the release will be made into a draft. |  |
| `discussion-category-name` | If specified, a discussion of the specified category is created and<br/>linked to the release.<br/> |  |
| `files` | Newline-separated list of files to upload (supports globbing).<br/>Missing files are ignored.<br/> |  |

[^required]: required

## Outputs

| Parameter | Description |
|-----------|-------------|
| `release-id` | The unique identifier of the release that was created.<br/>See https://docs.github.com/en/rest/releases/releases#get-a-release<br/> |

## Examples

### Publish release when new tags are pushed

```yaml
name: Publish release

on:
  push:
    tags:
      - "*"

jobs:
  publish-release:
    runs-on: ubuntu-latest
    if: startsWith(github.ref, 'refs/tags/v')
    steps:
      - name: Checkout branch
        uses: actions/checkout@v3
      - name: Build
        run: |
          echo "stuff!" > file1.txt
          echo "more stuff!" > file2.txt
          echo "even more stuff!" > even_more_stuff.txt
          echo "even more stuff! (#2)" > even_more_stuff_v2.txt
      - uses: notpeelz/action-gh-create-release@v5.0.1
        with:
          strategy: existing
          tag: ${{ github.ref_name }}
          title: ${{ github.ref_name }}
          files: |
            file*.txt
            even_more_stuff{,_v2}.txt
```

### Publish release via manual workflow run

```yaml
name: Create release

on:
  workflow_dispatch:
    inputs:
      version:
        description: "Version"
        required: true
        type: string

jobs:
  create-release:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout branch
        uses: actions/checkout@v3
      - uses: notpeelz/action-gh-create-release@v5.0.1
        with:
          strategy: fail-fast # this is the default
          # TODO: it's probably a good idea to validate the version format
          # in an earlier step.
          tag: v${{ inputs.version }}
          title: v${{ inputs.version }}
```
