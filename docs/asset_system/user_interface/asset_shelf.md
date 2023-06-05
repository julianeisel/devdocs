# Asset Shelf

An Asset Shelf provides fast and convenient access to assets from specific editors, without having to open an Asset Browser. It is optimized for using assets for a specific task (for example animating with pose assets, or painting with brush assets), and as such is more limited in functionality than the Asset Browser, but more efficient for certain contexts of use.

![An asset shelf showing material assets](../img/asset_shelf.png)

The asset shelf uses the _All_ asset library, and makes use of [asset catalogs](../backend/asset_catalogs.md) for efficient navigation within it.

Not all editors support asset shelves. Enabling them for a new editor requires some work, as explained [below](#adding-asset-shelf-support-to-an-editor).

## Source Code Locations

Most asset shelf code can be found in:

- `source/blender/editors/asset/intern/asset_shelf_xxx.cc`: Main asset shelf source files.
- `source/blender/editors/asset/ED_asset_shelf.h`: API to integrate asset shelves in editors.
- TODO (asset shelf type, DNA, RNA)

## Creating Asset Shelves

To display an asset shelf in a specific context, and for specific kinds of assets, a new asset shelf type has to be registered:
```.py
class VIEW3D_AST_sculpt_brushes(bpy.types.AssetShelf):
    bl_space_type = "VIEW_3D"

    @classmethod
    def poll(cls, context):
        return bool(context.object and context.object.mode == 'SCULPT')

    # Temporary design!
    @classmethod
    def asset_poll__(cls, asset):
        return asset.file_data.id_type == 'BRUSH'
```

This will display an asset shelf in sculpt mode, for assets that are of ID type `BRUSH`.

> WARNING:
> The `asset_poll__` method is meant as a temporary design solution. In future this should be handled via a `bl_traits` dictionary or list (i.e. `bl_traits = {'BRUSH'}`). Blender's Python API doesn't support this syntax for defining a collection of strings using dictionaries or lists yet.

A template for this can be found in the Text Editor under _Templates_ > _Python_ > _Ui Asset Shelf_.

### Under the Hood

Note that the asset shelf type is registered for a specific editor type, *3D View* in the example above. When an editor of this type is shown, its registered asset shelf types are polled on every redraw. Once the `poll()` of an asset shelf type returns `True`, the system either restores a previously instanced asset shelf of this type, or creates a new one if needed.

#### Multiple Available Asset Shelves

It is possible that multiple asset shelf types are available, i.e. their `poll()` returns `True` in a given context. The system handles this, but only one shelf can be visible in an editor at a time. This is considered to be the *active* shelf. The user could be presented with a selector to choose an active asset shelf type when necessary, but this isn't implemented yet.

Previously active shelves are kept in memory. When the active shelf becomes unavailable, these previously active ones get polled in order from most recently to least recently activated. So as context changes, the most recently used asset shelves get priority in determining the new shelf to activate. Otherwise, the first registered asset shelf type that is available will be used to create a new asset shelf to activate from.

> INFO: **Alternative Solution**
> The alternative would be merging settings from multiple asset shelves into a runtime asset shelf, and split them again by type for writing to files. This could be done but doesn't seem necessary for now.

### Customizing Asset Shelf Behavior

#### Custom Drag & Drop

#### Custom Context Menu

## Regions

The asset shelf is split in two regions:

__Main Region__
: The region displaying the assets in a grid-view  (see [views](https://wiki.blender.org/wiki/Source/Interface/Views) documentation).

: Region types: `RGN_TYPE_ASSET_SHELF` in C, `ASSET_SHELF` in Python.

__Footer Region__
: Contains display options, filter controls and the catalog tabs.

: Region type: `RGN_TYPE_ASSET_SHELF_FOOTER` in C, `ASSET_SHELF_FOOTER` in Python.

## UI components

### Asset View

See `asset_shelf_asset_view.cc`.

The main region displays assets in a grid-view using the UI [view](https://wiki.blender.org/wiki/Source/Interface/Views) system for its layout. It defines default drag controllers, and calls the necessary callbacks of the asset shelf type, such as the `draw_context_menu()` method.

### Asset Catalog Selector

See `asset_shelf_catalog_selector.cc`.

The asset catalog selector uses a [tree-view](https://wiki.blender.org/wiki/Source/Interface/Views/Tree_Views) for its layout.

## Storage

Asset shelves store their data mostly through three structs:

**`AssetShelf`**
: Data of a single shelf, created by activating a shelf of a given `AssetShelfType`. Holds type information and settings, whereby settings are stored in a nested `AssetShelfSettings` member.

**`AssetShelfSettings`**
: The settings for an `AssetShelf` instance such as the enabled asset catalogs and other display options.
: See `asset_shelf_settings.cc`.

**`AssetShelfHook`**
: Container for all permanent asset shelf data. This contains a list of `AssetShelf` instances (as in, the storage of previously active asset shelves) as well as a pointer to the active asset shelf. On redraws, the active shelf is queried for availability in the current context, and the pointer updated if necessary.

These structs are part of SDNA so they can be written to .blend files.

## Adding Asset Shelf Support to an Editor

Making Asset Shelves available in an editor is straight forward, but requires some work still. The `ED_asset_shelf.h` header contains most public functions required for that.

TODO

- Add `AssetShelfHook` to the space data of the editor, and call the duplicate, file read and file write functions from the necessary places.

## FAQ

**Is it one asset shelf or multiple?**

: For the user, it is usually enough to refer to the two regions at the bottom with their contents as *asset shelf*. In more precise language, these can be considered the asset shelf regions, with contents defined by the active asset shelf data and the asset shelf type it was instantiated from. So multiple asset shelves are kept in storage (each of a different type), but only one is active (as in: visible) at a time.
