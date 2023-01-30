# Asset Catalogs

An asset can be assigned a catalog. Similar to files on disk, where a file can only exist in a single directory at a time (ignoring hard-links), an asset can only be associated with a single catalog at a time.

These catalogs are independent of the on-disk organisation of asset library files. This means that assets can be freely moved between blend files while staying in the same catalog, and vice versa.

*Introduced in [Blender 3.0](https://wiki.blender.org/wiki/Reference/Release_Notes/3.0/Asset_Browser).*

## Components of a Catalog

Each catalog consists of a ''catalog path'', a ''UUID'', and a ''simple name''.

### Catalog Path

The path of a catalog determines where in the catalog hierarchy the catalog is shown. Examples are `Characters/Ellie/Poses/Hand` or `Kitbash/City/Skyscrapers`.

Multiple catalogs can have the same path; in other words, multiple UUIDs can map to the same path. This is likely to happen with generic paths like `Materials/Metal` when combining assets from various sources. These catalogs are unified and shown as one catalog in the user interface.

### UUID

Each catalog has a UUID. This is what is stored in the asset, and what determines the "identity" of the catalog. As a result, a catalog can be renamed or moved around (i.e. you can change its path), and all assets that are contained in it will move along with it. This only requires a change to the catalog itself, and not to any asset blend file.

When creating a new catalog, Blender assigns it a [random UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier#Version_4_(random)). However, any valid, [non-nil UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier#Nil_UUID) can be used for catalogs. An asset can be assigned to the [nil UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier#Nil_UUID), which means it's not contained in any catalog.

### Simple Name

Each catalog has an optional ''simple name''. This name is stored along with the UUID in each asset. The purpose is to make it possible for humans to recognise the catalog the asset was assigned to, even when the ''catalog definition file'' (see below) is lost.

## Catalog Definition Files

Asset catalogs are stored in Catalog Definition Files (CDFs). Currently, each asset library has one CDF, `blender_assets.cats.txt`.

### Format

Catalog Definition Files (CDFs) are relatively simple text files, encoded in UTF-8.

Each CDF consists of a version indicator, and a line of text per catalog.

Each catalog line is colon-separated, of the form `{UUID}:{path}:{simple name}`.

Multiple catalogs can have the ''same path'', as described above. If there are multiple catalog definitions with the ''same UUID'', the first one "wins" and the rest is ignored.

### Examples

For example, this is a valid catalog definition file:
```
# This is an Asset Catalog Definition file for Blender.
#
# Empty lines and lines starting with `#` will be ignored.
# The first non-ignored line should be the version indicator.
# Subsequent lines are of the format "CATALOG_UUID:catalog/path/for/assets:simple catalog name"

VERSION 1

cd66bf52-58f4-45cb-a4e2-dc0e0ee8f3fe:/character/Elly/poselib:POSES_ELLY

  4eb44ec6-3424-405b-9782-ca006953e799:character/Elly/poselib/white space:POSES_ELLY_WHITESPACE

ee9c7b60-02f1-4058-bed6-539b8d2a6d34:/character/Elly/poselib/:POSES_ELLY_TRAILING_SLASH

b63ed357-2511-4b96-8728-1b5a7093824c:character/Ružena/poselib:Ružena pose library
fb698f2e-9e2b-4146-a539-3af292d44899:character/Ružena/poselib/hand:Ružena hands
dcdee4df-926e-4d72-b995-33106983bb9a:character/Ružena/poselib/face:Ružena face

313ea471-7c81-4de6-af81-fb04c3535d0e:catalog/without/simple/name
```

After reading it, Blender will reformat it to look like this:
```
# This is an Asset Catalog Definition file for Blender.
#
# Empty lines and lines starting with `#` will be ignored.
# The first non-ignored line should be the version indicator.
# Subsequent lines are of the format "CATALOG_UUID:catalog/path/for/assets:simple catalog name"

VERSION 1

313ea471-7c81-4de6-af81-fb04c3535d0e:catalog/without/simple/name:
ee9c7b60-02f1-4058-bed6-539b8d2a6d34:character/Elly/poselib:POSES_ELLY_TRAILING_SLASH
cd66bf52-58f4-45cb-a4e2-dc0e0ee8f3fe:character/Elly/poselib:POSES_ELLY
4eb44ec6-3424-405b-9782-ca006953e799:character/Elly/poselib/white space:POSES_ELLY_WHITESPACE
b63ed357-2511-4b96-8728-1b5a7093824c:character/Ružena/poselib:Ružena pose library
dcdee4df-926e-4d72-b995-33106983bb9a:character/Ružena/poselib/face:Ružena face
fb698f2e-9e2b-4146-a539-3af292d44899:character/Ružena/poselib/hand:Ružena hands
```

## Valid Catalog Paths

Catalog paths in the catalog definition files have to follow the following rules:

* All paths are absolute, so there is no difference between `/a/b` and `a/b`.
* Only `/` as separator (no `\`; think less filesystem path and more URL).
* Not empty (it's required for a valid catalog).
* No empty components (so not `a//b`; `a/b` is fine).
* Invalid characters: `:`, `\`.
* Paths are always interpreted as UTF-8.

When the asset system reads the catalog definitions, or when the paths where changed in another way (e.g. by renaming catalogs in the UI), the paths are "normalized" by doing the following:

* Remove any leading or trailing `/`
* `/` characters are escaped by replacing them with `\/`

The asset system code only uses this "normalized" version of the path.

## Writing to Disk

Catalogs are written to disk when the blend file is saved (this is still under consideration and can change). At this moment only a single catalog definition file (CDF) per asset library is supported; CDFs are always named `blender_assets.cats.txt`.

The location of the CDF is chosen as follows. The first rule that describes the situation "wins" and determines the location:

1. If the catalogs were loaded from disk (for example by selecting an existing library with existing catalogs in the asset browser), any changes/additions are written to the same file.
1. If the blend file is saved in a known asset library (i.e. configured in the preferences), any changes/additions are written to the top-level directory of that asset library.
1. Otherwise the CDF is saved in the same directory as the blend file.
