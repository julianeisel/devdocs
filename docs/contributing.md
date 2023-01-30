# Contribute Documentation

This documentation is built using [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/).

## Building this Documentation

### 1. Get the Sources

In a directory of your choice (assumes `git` is installed):
```bash
$ git clone https://github.com/julianeisel/devdocs.git
$ cd devdocs
```

### 2. Install Prerequisites

Install Material for MkDocs and all the necessary extensions/plugins:

```bash
python -m pip install mkdocs-material markdown-callouts mkdocs-section-index pygments pymdown-extensions mkdocs-glightbox mkdocs-git-revision-date-localized-plugin
```
You may have to use `python3` instead of `python`.

### 3. Build

#### Build and Live Reload

While working on the documentation, it is useful to use the life reloading feature from MkDocs:
```bash
mkdocs serve
```
This will print a URL under which you will find the latest build of your documentation that is updated continuously while your working on it.

#### Build only

To only build the documentation without serving it:
```
mkdocs build
```

## Adding Pages

When adding a page, make sure to register it in the `mkdocs.yml`, otherwise it will not show up.

## Adding/Enabling Extensions & Plugins

Extensions and plugins are controlled via the `mkdocs.yml` file. If additional Python modules are necessary for one, it needs to be added to the following places:

- In the `pip install` command of the instructions above [above](#build-this-documentation)
- The GitHub Actions workflow configuration in `.github/workflow/ci.yml`, to deploy the generated documentation HTML.

## Configuring (Settings, Extensions, Plugins, etc.)

Settings, including the enabled extensions and plugins are controlled via the `mkdocs.yml` file.

## License

The content of this documentation is available under a [Creative Commons
Attribution-ShareAlike 4.0 International
License](https://creativecommons.org/licenses/by-sa/4.0/) or any later version.
Excluded from the CC-BY-SA are also the used logos, trademarks, icons, source
code and Python scripts.
