Device configuration for Sony Xperia XZ1 Compact (lilac)
========================================================

Description
-----------

This repository is for LineageOS 17.1 on Sony Xperia XZ1 Compact (lilac).

How to build LineageOS
----------------------

* Make a workspace:

    ```bash
    mkdir -p ~/lineageos
    cd ~/lineageos
    ```

* Initialize the repo:

    ```bash
    repo init -u git://github.com/LineageOS/android.git -b lineage-17.1
    ```

* Check out a copy of this project, and symlink its manifests for use by repo:

    ```bash
    git clone https://github.com/foresto/lineageos_lilac
    mkdir -p .repo/local_manifests
    cd .repo/local_manifests
    ln -s ../../lineageos_lilac/manifests/* .
    cd -
    ```

* Sync the repo's projects:

    ```bash
    repo sync
    ```

* Dump the contents of a stock firmware image

    Xperia firmware image file names vary by region.  The version we need will be named something like `G8441_*_47.2.A.11.228-*`

    xxx todo: explain the unpacking process / link flamefire's dump-stock.md

* Copy dumped firmware blobs into place for the build

    ```bash
    cd device/sony/lilac
    ./extract-files.sh /path/to/dumped/firmware
    ```

* Configure the build environment

    xxx todo: move this to just before the build step, unless patches need it, in which case just move lunch

    ```bash
    source build/envsetup.sh
    lunch lineage_lilac-userdebug
    ```

* Apply patches

    Some of the patches in this repo fix a few bugs or issues in LineageOS while others make the build deviate a lot from the "vanilla build".

    To apply all available patches:  (This is only for advanced users!)

    ```bash
    device/sony/lilac/patches/applyAllPatches.sh
    ```

    To apply only the required patches and security fixes:

    ```bash
    device/sony/lilac/patches/applyBasicPatches.sh
    ```

* Get newer Clang compiler(s)

    For better performance/battery life, we use a newer version of the Clang compiler.
    To make the build work with it, apply these patches (unless all patches were applied in the previous step):

    ```bash
    device/sony/lilac/patches/applyPatches.sh device/sony/lilac/patches/allow-newer-kernel-clang.patch
    device/sony/lilac/patches/applyPatches.sh device/sony/lilac/patches/update-kernel-clang-for-host-cc.patch
    ```

    Alternatively, the old Clang version can be used by following these steps:

    * Comment out the `TARGET_KERNEL_CLANG_VERSION :=` line in
      device/sony/yoshino-common/BoardConfigPlatform.mk

* Optionally, configure a compiler cache directory, to speed up future builds:

    ```bash
    mkdir -p "/path/to/ccache/dir"
    export CCACHE_DIR="/path/to/ccache/dir"
    cat >> $CCACHE_DIR/ccache.conf <<EOF
    compression = true
    max_size = 30G
    <<EOF
    export USE_CCACHE=1
    export CCACHE_EXEC=/usr/bin/ccache
    ```

* Build LineageOS

    ```bash
    make clean
    mka bacon
    ```

When complete, the built files will be in the `out/target/product/lilac` directory.
