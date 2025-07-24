# Directus Tab Group Interface

A tab group interface for Directus. This interface allows you to group fields into tabs as an alternative to
the default accordion group. This is a fork from [hanneskuettner/directus-extension-group-tabs-interface](https://github.com/hanneskuettner/directus-extension-group-tabs-interface) and uses the same id in Directus.

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/timio23/directus-group-tabs-interface/main/docs/screenshot-dark.png">
  <img alt="Screenshot of the tab group interface" src="https://raw.githubusercontent.com/timio23/directus-group-tabs-interface/main/docs/screenshot-light.png">
</picture>

## What's New in v3

* Support for unlimited tabs
* Support for smaller screens
* Updated dependancies
* Re-enabled field key input for user friendly appearence in filters

## Installation

Add `directus-extension-group-tabs-interface` to your project:


```shell
# Using pnpm
pnpm add @timio23/directus-group-tabs
# Using yarn
yarn add @timio23/directus-group-tabs
# Using npm
npm install @timio23/directus-group-tabs
```

## Usage

When creating a new field you can select `Tab Group` in the `Groups` section.

Alternatively you can change the interface of an existing group field in the
`Interface` section.

### Options

- `Overwrite Group Width`: If checked, force Directus to display the tab list in the "Fill Width" mode.
Normally, the width option is not available for groups, but so far no problems have been encountered. Please open an
issue if you encounter any visual bugs.

