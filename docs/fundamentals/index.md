# Important Concepts

## What is an _Asset_ anyway?

The [Blender reference manual gives a user level answer](https://docs.blender.org/manual/en/latest/files/asset_libraries/introduction.html#what-is-an-asset) to this question. As far as the asset system design goes, __assets are arbitrary entities that are packaged for organized sharing/reuse__.

The term _entity_ is used here, because the design is meant to support assets that are not [Blender data-blocks](https://docs.blender.org/manual/en/latest/files/data_blocks.html) (also called [IDs or ID Datablocks on a technical level](https://wiki.blender.org/wiki/Source/Architecture/ID/ID_Type)), even if the current implementation is limited to that. In future it should be possible to let the asset system deal with any data as assets, like files on disk, USD prims, SQL data-base entities, data fetched from the web, etc. This only works because the asset system doesn't deal with the actual underlying entity itself (the object, the material, the brush, etc.). It only deals with the package of the entity.

## Asset Representation

If assets are packaged entities, what does the package look like? __An _asset representation_ is the package for an asset, which enables the asset system to work with it.__ When loading an asset library, an asset representation is created for each detected asset and put into the asset library storage.

Note that an asset representation is just the package itself, and usually doesn't contain the actual entity. It contains information on how/where to find the entity, so that the asset can be loaded when the user asks for it.

## A New Core (non-`Main`) Database

At Blender's core design, there is the `Main` database to store the data-blocks for the current file, accessible for all of Blender. Through the storage of the asset libraries, __the asset system effectively provides another global data-base, but for entities that may not be part of the current file__.

Unlike `Main`, the asset library storage is not meant to be written to files (although an asset index is written to improve loading speeds).
