# Reusable GitHub Workflows

This repository contains reusable GitHub Actions workflows for PHP projects.

## Concurrency Control

To prevent race conditions when multiple workflows attempt to commit to the repository at the same time, we use a concurrency group called `auto-commit-${{ github.ref }}`. This ensures that only one workflow that performs an automatic commit can run at a time on a given branch. This lock is applied to the following workflows:

- `fix-code-style-issues.yml`
- `label-driven-release.yml`

When using these workflows, you must add the `permissions` block to your job to grant the necessary write access.

## Available Workflows

### run-tests.yml
Runs PHP tests across multiple PHP versions, Laravel versions, and operating systems.

**Usage:**
```yaml
name: Tests
on: [push, pull_request]
jobs:
  test:
    uses: reshadman/common-github-workflows/.github/workflows/run-tests.yml@main
    with:
      php-versions: '["8.2", "8.3", "8.4"]'
      laravel-versions: '["11.*", "12.*"]'
```

### phpstan.yml
Runs PHPStan static analysis.

**Usage:**
```yaml
name: PHPStan
on: [push, pull_request]
jobs:
  phpstan:
    uses: reshadman/common-github-workflows/.github/workflows/phpstan.yml@main
    with:
      php-version: '8.4'
```

### fix-code-style-issues.yml
Automatically fixes code style issues for PHP, Blade, JS, and CSS.

**Usage:**

To use this workflow, you need to have the corresponding configuration files in the root of your project:
- **PHP:** `pint.json` or `.php-cs-fixer.dist.php`
- **Blade:** `.bladeformatterrc`
- **JS/CSS:** `.prettierrc`

```yaml
name: Fix Code Style
on: [push, pull_request]
permissions:
  contents: write
jobs:
  fix-style:
    uses: reshadman/common-github-workflows/.github/workflows/fix-code-style-issues.yml@main
    with:
      fix-php: true
      fix-blade: true
      fix-js-css: true
      blade-paths: 'resources/views/**/*.blade.php'
      js-css-paths: 'resources/css/**/*.css resources/js/**/*.js'
```

### label-driven-release.yml
Creates releases based on PR labels (release:major, release:minor, release:patch).

**Usage:**
```yaml
name: Release
on:
  pull_request:
    types: [closed]
permissions:
  contents: write
jobs:
  release:
    uses: reshadman/common-github-workflows/.github/workflows/label-driven-release.yml@main
```
