# Editing

## Live Editing

When editing in a local build of this documentation, it's recommended to use [live reloading](building.md#live-edit). This automatically refreshes your browser to reflect changes when you save an edited file.

## Adding Pages

When adding a page, make sure to register it in the `nav` section of `mkdocs.yml`. Otherwise the page will not show up for navigation.

## Adding/Enabling Extensions & Plugins

Extensions and plugins are controlled via the `mkdocs.yml` file. If additional Python modules are necessary for one, they must be added to the `requirements.txt` file.

## Configuring (Settings, Extensions, Plugins, etc.)

Settings, including the enabled extensions and plugins are controlled via the `mkdocs.yml` file.
