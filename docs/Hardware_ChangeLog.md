# Hardware Change Log (SC940D)

This document tracks the revision history of the SC940D PCB as extracted from design files.

| Release | Description | Criticality |
| :--- | :--- | :--- |
| **Release 16.0** | **Current Version**. Added Pad/Via hole size tolerance values. | **Caution** |
| **Release 15.1** | Support for Multi-line PCB Text added. Note: verify text rendering on older CAM software. | Caution |
| **Release 15.0** | Support for separate solder masks for top & bottom of pads added. | Caution |

## Legacy / Unverified Changes
> [!NOTE]
> Older revisions (Pre-15.0) are not detailed in the current export. Please refer to archive files in `SC940D/_Archives` if available.

## Known Issues (Current Design)
-   **Documentation Gap**: The "Coeur" module (`SC943`) lacks a detailed schematic in the main project tree.
-   **Designator Jump**: Component designators skip values (e.g., Relays K1-K17, then gap?), typical in evolved designs.
