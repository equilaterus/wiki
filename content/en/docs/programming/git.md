---
title: "Git"
description: "Quick-starts and guidelines for .Net Core developemnt."
lead: "Quick-starts and guidelines for .Net Core developemnt"
date: 2024-23-04
lastmod: 2024-23-04
draft: false
images: []
menu:
  docs:
    parent: "programming"
weight: 10
toc: true
---

> We always recommend to use a GUI tool on top of your *git* repo. Check GitHub Desktop for [Linux](https://equilaterus.com/wiki/docs/linux/desktop/#github-desktop) or [Windows/Mac](https://desktop.github.com/).

## Git submodules

If you need to share internal code and/or include other libraries (like ImGUI for C/C++) you may consider using [Git Submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules).

```sh
git submodule add {REPO_URL} {TARGET_FOLDER}


# Example
git submodule add https://github.com/ocornut/imgui libs/imgui/
```

**Important:**

* We recommend to use GitHub Desktop and add the submodule itself (after cloning the project) as a repo with an alias (in GitHub Desktop).

* For the submodule we recommend using a branching strategy to easily go back and forth between versions. For example: you can create a branch for each stable version and have branches.

## Git LFS

To enable **Git LFS** on your project, first check your repository provider availability and costs. After that follow these steps:

1. Install:
   ```sh
   git lfs install
   ```

2. Create a file on your repo root folder named **.gitattributes** and customize its contents using this sample:

   ```
   # Unreal Engine file types.
   # Note the lockable flag.
   *.uasset filter=lfs diff=lfs merge=lfs -text lockable
   *.umap filter=lfs diff=lfs merge=lfs -text lockable

   # Common Raw Content file types.
   *.blend filter=lfs diff=lfs merge=lfs -text binary
   *.fbx filter=lfs diff=lfs merge=lfs -text binary
   *.3ds filter=lfs diff=lfs merge=lfs -text binary
   *.psd filter=lfs diff=lfs merge=lfs -text binary
   *.ai filter=lfs diff=lfs merge=lfs -text binary
   *.svg filter=lfs diff=lfs merge=lfs -text binary
   *.png filter=lfs diff=lfs merge=lfs -text binary
   *.xcf filter=lfs diff=lfs merge=lfs -text binary
   *.bmp filter=lfs diff=lfs merge=lfs -text binary
   *.jpg filter=lfs diff=lfs merge=lfs -text binary
   *.jpeg filter=lfs diff=lfs merge=lfs -text binary
   *.tif filter=lfs diff=lfs merge=lfs -text binary
   *.tiff filter=lfs diff=lfs merge=lfs -text binary
   *.tga filter=lfs diff=lfs merge=lfs -text binary
   *.targa filter=lfs diff=lfs merge=lfs -text binary
   *.mp3 filter=lfs diff=lfs merge=lfs -text binary
   *.wav filter=lfs diff=lfs merge=lfs -text binary


   # Anything in `/RawContent` dir.
   /RawContent/**/* filter=lfs diff=lfs merge=lfs -text binary
   ```

### Git LFS Commands

* After *staging* files to your Git Repo (using the ADD command) you can review which files are going to be uploaded to the Git LFS server suing this command:

  ```
  git lfs status
  ```

* To list files being tracked by git-lfs, run:

  ```
  git lfs ls-files
  ```

* To list files not being tracked by git-lfs (after commit), run on *git bash*:

  ```
  { git ls-files && git lfs ls-files | cut -d' ' -f3-; } | sort | uniq -u
  ```

### Git LFS more information

* [Git LFS tutorial](https://github.com/git-lfs/git-lfs/wiki/Tutorial)

* [Git LFS File locking](https://github.com/git-lfs/git-lfs/wiki/File-Locking).

* [Listing files not tracked by Git LFS](https://stackoverflow.com/questions/42963854/list-files-not-tracked-by-git-lfs)

## FAQ

* ***Warning message*: This diff contains a change in line endings from 'LF' to 'CRLF'.** Run this command on your repo:

  ```sh
  git config core.autocrlf input
  ```
