# Asset System

__The asset system brings a native understanding of assets (data-blocks packaged for sharing/reuse) to Blender's core design, and enriches it with a number of features for great asset based workflows.__

For example it includes: The asset browser, asset shelfs, asset libraries, asset library loading, asset catalogs, asset metadata, etc.

The [backend](backend/index.md) implements all the core types and functionality, which various parts Blender can access. The design is user experience driven, and as such, the backend very much serves the [user interface](user_interface/index.md). They work in close collaboration to provide an experience that makes assets feel like first-class citizens in Blender.

NOTE:
The asset system is still in early development so expect this documentation to receive regular updates. In various places, it will refer to designs that are not there yet (at least not in the `master` branch), partially there or just temporary. This will be clearly indicated.
