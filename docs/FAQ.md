= Asset System FAQ =

Common questions regarding the asset system, and how to use it on the technical side. This is not meant as artist level documentation, such questions should be answered [https://docs.blender.org/manual/en/latest/files/asset_libraries/index.html in the user manual].

=== How to port my asset add-on to the Asset Browser/System? ===

One of the main goals of the asset project is to bring a standardized user and developer interface for a consistent asset browsing experience. There are numerous add-ons that implement their own asset management/browsing UI. Via the asset system, everybody should be able to use the native Asset Browser as the main place to access assets instead. This includes assets stored on the web, with on-demand downloading.

The asset system is still in early stages of development, and the Python API is only in the initial planning phases. That means, although this is a main goal of the overall project, we are by far not there yet. Blender is currently only able to recognize data-blocks as assets stored on the user's hard drive. This may already be enough for your use-case. Users or an add-on can download .blend files containing assets, and the `blender-assets.cats.txt` to some directory. This directory can then be [https://docs.blender.org/manual/en/latest/files/asset_libraries/introduction.html#what-is-an-asset-library registered as an asset library]. There is also a way to display assets that are not stored on the hard drive. For that an add-on can generate .blend files with dummy data-block assets (with metadata and previews). After dragged into the scene, the add-on can download the actual asset and replace the dummy one with it. Many existing asset add-ons already worked this way, so it should be easy to move towards the Asset Browser. This is a workaround for until there is a proper asset system API for add-ons to use.

== Python API ==

At this point, there is no proper Python API for the asset system yet, it is still in the planning. There are still ways to achieve commonly requested functionality through other parts of the Python API however.

=== How do I load custom previews? ===

There is no simple `asset.load_custom_preview(filepath)` method or similar available. But there are still two ways to do the job:
* [https://docs.blender.org/api/current/bpy.ops.ed.html#bpy.ops.ed.lib_id_load_custom_preview `bpy.ops.ed.lib_id_load_custom_preview()`]: Attempts to load an image file from a `filepath` property (opens a File Browser if not set) to the active ID set in context.<br/>For example:<source lang="py">
override = context.copy()
# Set context "id" member to some ID, e.g. a material.
override["id"] = my_material
with context.temp_override(**override):
   bpy.ops.ed.lib_id_load_custom_preview(filepath="path/to/image.png")
</source> Or to call the operator from a button: <source lang="py">
# Set layout context "id" member to some ID, e.g. a material.
layout.context_pointer_set("id", my_material)
props = layout.operator("ed.lib_id_load_custom_preview")
props.filepath = "path/to/image.png"
</source>
* [https://docs.blender.org/api/current/bpy.types.ID.html#bpy.types.ID.preview `bpy.types.ID.preview`]: ID assets (and currently all assets have to be IDs) share the ID's preview image. So the preview can be manipulated via the [https://docs.blender.org/api/current/bpy.types.ImagePreview.html#bpy.types.ImagePreview `bpy.types.ImagePreview`] API. This allows loading a custom buffer into the preview.<br/>For example:<source lang="py">
buffer = ... # Some 32-bit RGBA buffer
# Overwrite buffer of a material's preview image.
# Important: Assumes the image size in the buffer matches `my_material.preview.image_size`!
my_material.preview.image_pixels = buffer
</source>
