# Building this Documentation

<!--
> INFO:
> The documentation uses the paid [Material for MkDocs Insiders](https://squidfunk.github.io/mkdocs-material/insiders/) tier now. However the documentation should be usable without this still. It is only used for the grid cards on the home page.
-->

## 1. Get the Sources

In a directory of your choice (assumes `git` is installed):
```bash
$ git clone https://github.com/julianeisel/devdocs.git
$ cd devdocs
```

## 2. Install Prerequisites

Install Material for MkDocs and all the necessary extensions/plugins:

```bash
python -m pip install mkdocs-material markdown-callouts mkdocs-section-index pygments pymdown-extensions mkdocs-glightbox mkdocs-git-revision-date-localized-plugin
```
You may have to use `python3` instead of `python`.

## 3. Build

#### Option 1: Build and Live Reload {#live-edit}

While working on the documentation, it is useful to use the live reloading feature from MkDocs:
```bash
mkdocs serve
```
This will print a URL under which you will find the latest build of your documentation that is updated continuously while your working on it.

#### Option 2: Build only

To only build the documentation without serving it:
```
mkdocs build
```

This will print a folder containing the `index.html` to open in a web browser.