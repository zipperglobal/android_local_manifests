## Building AOSP Android for Zipper Devices

This guide takes you through building AOSP for Zipper devices. This guide is based upon the Sony Xperia Open Devices program. On this page you can find instructions on building AOSP Nougat 7.1.1 for the Sony Xperia X with the Zipper platform included into the build.

### Preparation
We presume *Ubuntu 16.04 LTS*, though other versions of *GNU/Linux* should work.

First we need to ensure you have the correct Java runtime and development environment.

```
sudo apt-get purge openjdk-\* icedtea-\* icedtea6-\*
sudo apt-get update
sudo apt-get install openjdk-8-jdk
```

Next we need the necessary tools to build the Android sources.

```
sudo apt-get install bison g++-multilib git gperf libxml2-utils make zlib1g-dev:i386 zip
```

## Downloading sources

Now we have the tools to build Android, we need to grab the Android sources using *repo* from Google.

Firstly, we download the repo tool:

```
mkdir ~/bin
curl http://commondatastorage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
chmod a+x ~/bin/repo
export PATH=~/bin:$PATH
```

Now to initialize the Android AOSP build directory.

```
mkir ~/android
cd ~/android
repo init -u https://android.googlesource.com/platform/manifest -b android-7.1.1_r55

cd .repo
git clone https://github.com/zipperglobal/android_local_manifests local_manifests -b n-mr1_4.4
cd ..
```

Next we download the main source code from Google, as well as the device specific parts from Sony's repository, and the Zipper platfrom from ours.

```
repo sync
./repo_update.sh
```

The above commands will take some time, so please be patient. The downloaded sources are about 40-60G and the build requires at least 60G of free space on top of that.

Once the sources are all downloaded, we can prepare the build environment for building our device image.

## Building

```
source build/envsetup.sh
lunch aosp_f5121-userdebug
```

Now that the sources are setup to build images for our Sony Xperia X devices, we can start the build process. This will take several hours, to start building your device image run:

```
make -j<insert cpu hardware thread count of your machine>
```

## Flashing

Once the build has completed successfully, you can flash the system by running. Before you flash your own custom images to your device. You must first make sure that the devices' bootloader has been unlocked, by using the Sony bootloader unlocking service found [here](https://developer.sonymobile.com/unlockbootloader/). Once you have unlocked your bootloader, you may use the below commands to flash your device:

```
fastboot –S 256M flash boot out/target/product/<device>/boot.img
fastboot –S 256M flash recovery out/target/product/<device>/recovery.img
fastboot –S 256M flash system out/target/product/<device>/system.img
fastboot –S 256M flash userdata out/target/product/<device>/userdata.img
```

You also need to obtain and install the vendor image for our device. Download the vendor OEM image from [here](https://developer.sonymobile.com/downloads/software-binaries/software-binaries-for-aosp-nougat-android-7-1-kernel-4-4-loire/). Then flash it using:

```
fastboot flash oem SW_binaries_for_Xperia_AOSP_N_MR1_4.4_v05_loire.img
```

Now you should be good to go, restart your device and it should now boot into Zipper :)

```
fastboot reboot
```
