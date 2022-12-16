# From File Browser to Asset System

__The asset system was born out of an earlier version of the asset functionality, which was entirely based on the File Browser. The transition from the file browser to the dedicated asset system is ongoing, with implications on the current code.__

## The File Browser as Asset Browser

The initial introduction of asset functionality focused on delivering an Asset Browser to deal with ID data-blocks that were marked as assets. The File Browser seemed like a good starting point, since it provided a similar frontend, and a backend that served similar needs (support for asynchronous recursive loading of directories and .blend file contents, asynchronous preview loading, filtering, ...). However, the File Browser started showing a number of significant issues, and requirements changed too: Now assets should be accessible outside of the asset browser as well.


>? NOTE: **Some Problems**
> Examples of problems the file browser based design was showing:
>
> - Scrolling through big libraries (a few thousand assets) would cause minute long freezes. Cause was the preview loading which tries to be smart and fails miserably. This is not easy to address in the current design.
> - Code was becoming difficult to manage. Too many `if (is asset browser) {...} else {...}` like bits and lots of special handling for assets.
> - Awkward Python API integration: Asset information needed to be available through BPY, which had to be done by exposing the fact that assets are implemented as files in the Python API. There was no other persistent enough type that could be used and there was no time to implement something proper. Main relicts of that are `AssetHandle` and `bpy.context.asset_file_handle`.
> - Changed requirements:  Now assets should become available anywhere in the UI, for example in search menus, add menus, or asset shelves that can be accessed within 3D Views and other editors. So global access to assets with efficient storage management was needed, the per-editor storage of the Asset/File Browser wasn't enough anymore.

Out of this, the asset system design was born. It would be a globally accessible system, optimized for the asset functionality wanted in Blender and ready for the future. 

__Asset functionality should be completely detached from the file browser, both backend and frontend. Instead the asset system should be developed further as basis for all asset functionality and UIs.__

## State of the Transition

Most of the backend responsibilities have been moved to the asset system already. The file browser still performs the loading and unloading of asset libraries and previews from disk, which should be replaced with the asset loader design ([T103188 - Asset System: Asset Library Loader](https://developer.blender.org/T103188)). Global access to assets can happen via the Asset List API, see below.

>? INFO: **More on how the file browser backend and asset system interact...**
> The file browser backend in [`filelist.cc`](https://developer.blender.org/diffusion/B/browse/master/source/blender/editors/space_file/filelist.cc) contains the functionality for recursive and asynchronous reading of asset libraries and previews from disk. In a thread, it first triggers loading of the basic asset library data (which includes loading asset catalog definition files from disk). It then reads all necessary data for individual assets from disk and passes that on to the asset library to create new asset representations. When the asset representations aren't needed anymore (e.g. when the asset browser is closed, or the asset library changed), it has to remove the asset representations again. So the file-list is entirely responsible for managing the lifetime of the asset representations that are stored in the asset libraries of the asset system.
> Previews are loaded in a separate pass, the asset system is not involved there yet.

For the frontend the asset browser still uses the file browser code. Work is ongoing to change that: The plan is to have the layouts be created through reusable standard components (so called [_views_](https://wiki.blender.org/wiki/Source/Interface/Views)), managed by the core user interface code. Both the file browser and asset browser would use this, greatly simplifying their code ([T95653: Split Asset and File Browser, implement Grid View](https://developer.blender.org/T95653)).

## Asset List API

The asset list API is an intermediate step towards the asset system. It is an asset centric abstraction for the file browser backend, and is meant to make assets and asset libraries globally accessible. If you want to access assets outside of the asset browser, this is the place to look at.

See [Backend > Asset List API](../backend/asset_list_api.md) for details.

## Designs to be Deprecated

As the transition progresses, some designs should be deprecated

- `AssetHandle`/[`bpy.types.AssetHandle`](https://docs.blender.org/api/current/bpy.types.AssetHandle.html#bpy.types.AssetHandle) - at least `AssetHandle.file_data` should be removed. An `AssetRepresentation` [TODO link] type is much better suited.
- [`bpy.context.asset_file_handle`](https://docs.blender.org/api/current/bpy.context.html#bpy.context.asset_file_handle)
- Asset list API: Currently serves as loader and accessor for asset library data only. Should be replaced with a proper asset library loader design. [TODO link]