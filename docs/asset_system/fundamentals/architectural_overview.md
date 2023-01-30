# Architectural Overview

The entire asset system can be stripped down to it's main components:

<!-- Abusing state diagrams a bit here :) -->
``` mermaid
stateDiagram-v2
  direction TB

  asset_system_backend: Asset System Backend

  asset_system_backend --> UI
  BPY --> asset_system_backend
  asset_system_backend --> BPY

  state asset_system_backend {
    direction LR

    asset_libraries: Asset Libraries
    asset_catalogs: Asset Catalogs
    asset_representations: Asset Representations

    asset_libraries --> asset_catalogs
    asset_libraries --> asset_representations
  }

  state UI {
    direction RL

    asset_browser: Asset Browsers
    asset_shelf: Asset Shelfs
    asset_views: Asset View Templates
  }

```

---

This is a more complete, but a bit outdated overview, including a number of helper classes:

![(Outdated) architectural overview](../img/Architecture%20Design%20-%20Planned%20Design%20(WIP).jpg)