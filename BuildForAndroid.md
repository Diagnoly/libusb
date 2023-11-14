# Introduction
This documentation explain how compiled `libusb.so` (cf. https://libusb.info/) for Android and replace the version used by Android Fetoly Applications (Fetoly Essential & Heart) in order to respect the LGPL 2.1 license.

# Compilation
## Download NDK
First download Android NDK 25.1 (= r25b). Here a direct download link: https://dl.google.com/android/repository/android-ndk-r25b-linux.zip

If the link has expired, this website list old NDK versions: https://github.com/android/ndk/wiki/Unsupported-Downloads

## Unzip NDK
Uncompress the download file `/android-ndk-r25b-linux.zip` to a folder (like `~/NDK`).
Check that you have this folder hierarchy: `~/NDK/android-ndk-r25b/README.md`, else move folder `android-ndk-r25b` (which is inside `android-ndk-r25b-linux`) to `~/NDK/`.

## Install CMake (required version >= 2.19)
If not present, launch these commands to instal `CMake`:
```
CMAKE_VERSION=3.26.2
mkdir cmake-${CMAKE_VERSION}
cd cmake-${CMAKE_VERSION}
wget https://github.com/Kitware/CMake/releases/download/v${CMAKE_VERSION}/cmake-${CMAKE_VERSION}-linux-x86_64.sh
chmod +x cmake-${CMAKE_VERSION}-linux-x86_64.sh
./cmake-$CMAKE_VERSION-linux-x86_64.sh --skip-license -prefix=.
sudo ln -f -s $(pwd)/bin/* /usr/bin
```

# Install git
If not present, launch this command to install `git`:
```
sudo apt update
sudo apt upgrade -y
sudo apt install -y git
```

## Compilation
Inside a bash/shell terminal, clone libusb git repo with modification from Diagnoly:
```
git clone https://github.com/Diagnoly/libusb.git
```

Set path to Android NDK 25.1 (= r25b):
```
export NDK=~/NDK/android-ndk-r25b
```

Compile the library for Android:
```
mkdir build
cd build
cmake -DCMAKE_TOOLCHAIN_FILE=$NDK/build/cmake/android.toolchain.cmake -DANDROID_ABI=arm64-v8a ..
cmake --build . -j ${nproc}
```

A file `libusb.so` has been created inside current folder.

# Replace Fetoly libusb library by your custom version
## Copy inside tmp folder
To replace Fetoly libusb.so version, you need to copy your version of `libusb.so` to the Android device inside `data/local/tmp/fetoly/libusb.so`.

To do that, the easiest way if to install Android Studio and use Device Explorer to upload the file.

### Install Android Studio
First, install Android Studio, following that guide: https://developer.android.com/studio/install

### Enable developper mode on the Android device
Then, temporary enable Developer mode on the Android Device, using that guide: https://developer.android.com/studio/debug/dev-options

### Connect the Android device to Android Studio
Once the developper mode is enabled, plug your device on your computer using an USB cable and enable USB debugging inside Developer Options. See that guide to enable USB debugging: https://developer.android.com/studio/debug/dev-options

### Upload libusb.so
And finally, inside Android Studio, open Device Explorer (see https://developer.android.com/studio/debug/device-file-explorer), go to folder `data/local/tmp/`, create a folder `fetoly` (right click on `tmp`, **New** > **Directory**), and inside `fetoly` upload your version of `libusb.so` (right click on `fetoly`, **Upload...** and select your custom version of libusb library).

### Launch Fetoly application
Now you can launch Fetoly application, your version of libusb.so will be used instead of the one delivered with Fetoly.

If Fetoly application is loaded with a custom version from `data/local/tmp/fetoly/libusb.so`, note that, inside the application, the USB icon in the bottom left will be rotate of 90°, i.e. oriented horizontally (instead of vertical orientation for default library).

## Another solution (more advanced)
Another solution is to retrieve the apk file of the Android application, extract its content, and replace libusb.so by your own version (inside folder `/lib/arm64-v8a/`), recreate the apk file and signed it with your own key.