---
title: "UE Linux"
description: "Unreal Development on Linux"
lead: "Unreal Development on Linux"
date: 2020-10-13T15:21:01+02:00
lastmod: 2020-10-13T15:21:01+02:00
draft: false
images: []
menu:
  docs:
    parent: "unreal"
weight: 10
toc: true
---

* Official binaries: [Download page](https://www.unrealengine.com/en-US/linux)
* Recommended IDE: [Rider Linux](https://www.jetbrains.com/rider/download/#section=linux).
* Recommended Git App: [Github Desktop for Linux](https://github.com/shiftkey/desktop).
* PlasticSCM: [Linux packages](https://www.plasticscm.com/plastic-for-linux)

## Recommended config

* Build max parallel actions: [See UE Programming section]({{< relref "ue-programming#build-max-parallel-actions" >}})

* If you're using PlasticSCM: [Equilaterus PlasticSCM+GitHub](https://github.com/equilaterus-gamestudios/PlasticSCM-GitHub-extension)

## Compile your own binaries

To compile and generate your own binaries:

1. Clone.
2. Setup.
3. Generate files (DO NOT MAKE).

4. Goto **Engine/Build/BatchFiles/**

   ```sh
   /RunUAT.sh BuildGraph -target="Make Installed Build Linux" -script=Engine/Build/InstalledEngineBuild.xml -clean -set:HostPlatformOnly=true -set:WithDDC=false -set:GameConfigurations="Development;Shipping"
   ```

   *If there are errors with dependencies*: Copy **Engine/Binaries/DotNET** (from source folders) to your compiled **LocalBuilds/Engine/Linux/Engine/Binaries/DotNET** ( folder). Rerun previous command.

6. Copy results from **LocalBuilds/Engine/Linux** folder to any other location.

7. Create or open a project with **{OtherLocation}/Engine/Binaries/Linux/UnrealEditor**. After opening it, you can use Rider or your IDE to modify it.

Common errors:

* Dependencies errors:
  * [Blog birost](https://blog.birost.com/a?ID=01650-81b216da-49aa-49a2-81f4-9b699aed1057)
  * [Unreal forum](https://forums.unrealengine.com/t/linux-build-missing-references/296487)

Other options:
* [Unreal containers installed build](https://unrealcontainers.com/docs/use-cases/linux-installed-builds)
* See Unreal Slackers on discord.
