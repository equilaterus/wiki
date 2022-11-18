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

## Instructions

* Extract Unreal Engine on any location, for example:
  ```
  $HOME/Unreal/VERSION/
  ```

* Go inside the extracted folder **Engine/Binaries/Linux** and execute the file: **UnrealEditor**.

* Open or create a project. After that, you can open it with Rider (or your IDE) and compile it as usual.

### Common issues

* **I have VSCodium instead of VSCode (or any other IDE):** just try to create a new C++ project and disable VSCode when Unreal asks for it.

* **Could not locate the assembly "Ionic.Zip.Reduced":** go to your **Engine/Binaries/DotNet/UnrealBuildTool** and locate the file **Ionic.Zip.Reduced**, duplicate that file into the parent folder **Engine/Binaries/DotNet/**.

* **Unreal Startup is extremely slow with a C++ project:** run (NOT debug) the project. Use debug mode only when you require to actually *debug the code*. It is a little bit unconfortable but your project will load pretty fast.

## Recommended config

* Build max parallel actions: [See UE Programming section]({{< relref "ue-programming#build-max-parallel-actions" >}})

* If you're using PlasticSCM: [Equilaterus PlasticSCM+GitHub](https://github.com/equilaterus-gamestudios/PlasticSCM-GitHub-extension)

## Compile your own binaries

To compile and generate your own binaries of **Unreal Engine**:

1. Prepare your GitHub account to see the repo: [See this guide](https://www.unrealengine.com/en-US/ue-on-github)

2. Clone or download from: [GitHub Repo](https://github.com/EpicGames/UnrealEngine).

3. After downloading, execute **Setup.sh**:

   ```sh
   ./Setup.sh
   ```

3. Generate files (but do not *make*, to avoid recompiling twice).

   ```sh
   ./GenerateProjectFiles.sh

   # Do not execute this command
   # make
   ```

4. Go to **Engine/Build/BatchFiles/**, and execute the following command:

   ```sh
   /RunUAT.sh BuildGraph -target="Make Installed Build Linux" -script=Engine/Build/InstalledEngineBuild.xml -clean -set:HostPlatformOnly=true -set:WithDDC=false -set:GameConfigurations="Development;Shipping"
   ```

   *If there are errors with dependencies*: Copy **Engine/Binaries/DotNET** (from source folders) to your compiled **LocalBuilds/Engine/Linux/Engine/Binaries/DotNET**. Rerun previous command.

6. Copy the results from **LocalBuilds/Engine/Linux** folder to any other location.

7. Open the editor executable, located at: **{any other location}/Engine/Binaries/Linux/UnrealEditor**.

Common errors:

* Dependencies errors:
  * **Could not locate the assembly "Ionic.Zip.Reduced":** go to your **Engine/Binaries/DotNet/UnrealBuildTool** and locate the file **Ionic.Zip.Reduced**, duplicate that file into the parent folder **Engine/Binaries/DotNet/**.
  * [Blog birost post](https://blog.birost.com/a?ID=01650-81b216da-49aa-49a2-81f4-9b699aed1057)
  * [Unreal forum thread](https://forums.unrealengine.com/t/linux-build-missing-references/296487)

Other options:
* [Unreal containers installed build](https://unrealcontainers.com/docs/use-cases/linux-installed-builds)
