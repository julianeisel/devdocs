# Fundamentals

## What is an _Asset_?

The [Blender reference manual gives a user level answer](https://docs.blender.org/manual/en/latest/files/asset_libraries/introduction.html#what-is-an-asset) to this question. As far as the asset system design goes, assets can be arbitrary entities that are packaged for organized sharing/reuse. The term _entity_ is used here, because the design is meant to support assets that are not [Blender data-blocks](https://docs.blender.org/manual/en/latest/files/data_blocks.html) (also called [IDs or ID Datablocks on a technical level](https://wiki.blender.org/wiki/Source/Architecture/ID/ID_Type)), even if the current implementation is limited to that. In future it should be possible to let the asset system deal with any data as assets, like files on disk, USD prims, SQL data-base entities, data fetched from the web, etc. This only works because the asset system doesn't deal with the actual underlying entity itself (the object, the material, the brush, etc.). It only deals with the package of the entity.

## Asset Representation

## A New Non-Main Database

__The asset system provides asset functionality and a global asset database that is separate from the _Main_ database in Blender__ (the data-base representing .blend file data). Note that (unlike _Main_), the asset system is not a direct database, since the asset system can hold multiple asset libraries. Assets are also not meant to be written to files by the asset system, unlike the data-blocks in _Main_ (the asset system does write asset information into an index though, for fast loading of asset libraries).

