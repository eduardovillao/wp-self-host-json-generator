# WP Self-Host Updater Generator

A GitHub Action for WordPress plugin and theme developers to enable self-hosted updates directly from GitHub. This Action simplifies the process of self-hosting your plugins by:

1. Generating and commititing a JSON file based on the `readme.txt`.
- The `JSON` file will be used by plugin/theme to check updates.
- The `JSON` file will be created on `wp-dist/data-json`.
2. Creating a new release with a `.zip` distribution file.
- The `.zip` file follows the folder structure requires by WordPress.
- You can define folders or files to be excluded from the `.zip` file. _To more details check the section [Preparing the Distribution File: Excluding Paths/Files from the ZIP](#preparing-the-distribution-file-excluding-pathsfiles-from-the-zip)_

Use this action to streamline the self-hosting process for your WordPress plugins and themes.

## How to Use

This repository provides everything you need to the GitHub Action to work seamlessly and the "server" side of the self-host working as expected. However, this is just the “server” side of the solution. To fully enable self-hosted updates, you’ll need to integrate a simple PHP file into your plugin or theme. This file will manages communication with the JSON and release files generated by this Action directly from your repository.

- **Quick Start Guide:** Explore our guide [How to Self-Host WordPress Plugins on Github and Deliver Updates](https://eduardovillao.me/how-to-self-host-wordpress-plugins-on-github-and-deliver-updates/) for an easy-to-follow walkthrough of the entire self-hosting setup. It covers integrating this GitHub Action with the required PHP file in your plugin to ensure seamless updates.

- **Required Integration:** Check out [WP Self-Host Updater Checker](https://github.com/eduardovillao/wp-self-host-updater-checker) to get the required PHP file to add in your plugin/theme. This is an essential step to enable the fully self-hosted update mechanism.

### Requirements

Before using this GitHub Action, ensure the following:

- **`readme.txt` File**: A valid WordPress plugin readme file must exist in the root of your repository.
- **GitHub Token**: The repository must allow write access for the GitHub Actions token. You need to define the necessary permissions in your workflow file (details below).

### Workflow Configuration

To use this Action, create a new workflow file in your repository:

1. Navigate to `.github/workflows/`.
2. Create a new file, e.g., `generate-dist-file.yaml`.
3. Add the following content to the file:

```yaml
name: Generate Dist File for Plugin
on:
  push:
    tags:
      - '*'

permissions:
  contents: write

jobs:
  generate-json-and-release:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4
        with:
          ref: main

      - name: Run WP Self-Host Update JSON Generator
        uses: eduardovillao/wp-self-host-updater-generator@main
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
```

### Important Notes

1. **Permissions**: Ensure that `contents: write` is defined in the workflow file for the `GITHUB_TOKEN`.
2. **Repository Checkout**: The first step must check out the repository, specifying the branch where the JSON file will be committed (`main` or `master`).
3. **Trigger Events**: Currently, the Action supports triggers only when a tag is created. Future versions may support additional trigger events.

---

### Preparing the Distribution File: Excluding Paths/Files from the ZIP

To exclude specific files or directories from the `.zip` file generated during the release process, configure a `.gitattributes` file in your repository. This file ensures that unnecessary files are omitted from the export.

Here’s an example of a `.gitattributes` file:

```gitattributes
# Exclude the wp-dist folder and its contents
wp-dist/ export-ignore

# Exclude hidden files and directories
.* export-ignore

# Exclude Git metadata
.git/ export-ignore

# Exclude local configuration files
*.env export-ignore
*.log export-ignore

# Exclude development and editor-specific settings
.vscode/ export-ignore
.idea/ export-ignore
*.iml export-ignore

# Exclude unnecessary documentation
*.md export-ignore

# Exclude test files and directories
tests/ export-ignore
*.spec.js export-ignore
*.test.js export-ignore

# Exclude CI configuration files
.github/ export-ignore
.gitlab-ci.yml export-ignore

# Exclude build configurations and dependencies
node_modules/ export-ignore
package-lock.json export-ignore
yarn.lock export-ignore
```