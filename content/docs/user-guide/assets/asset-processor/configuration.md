---
linkTitle: Configuration
title: Asset Processor Configuration
description: Configure Asset Processor for Open 3D Engine (O3DE) with AssetProcessorPlatformConfig.setreg.
weight: 300
toc: true
---

You can modify the engine settings registry file for **Asset Processor** in `Registry/AssetProcessorPlatformConfig.setreg` to perform the following tasks:

* Add new source asset types for Asset Processor.
* Update existing file type rules.
* Update the ignore list.
* Add or remove target platforms.

    {{< note >}}
Asset Processor automatically builds assets for the host platform. For example, if Asset Processor is running on Windows, Asset Processor builds Windows assets even if pc is not enabled in the `.setreg` file.
    {{< /note >}}

* Add scan directories for Asset Processor.

* Set the number of process jobs.
* Specify *metafile fingerprinting*, that is, which files trigger related files to be rebuilt.

The `Registry/AssetProcessorPlatformConfig.setreg` is well commented and has many syntax examples for the available configuration options. To add project-specific overrides, you can add an `AssetProcessorGamePlatformConfig.setreg` file to your projects’s `Registry` directory. This file is read after the engine `AssetProcessorGamePlatformConfig.setreg` configuration file and can have additional project-specific settings for the ignore list, platforms, and file types.