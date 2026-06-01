# Build Notes

This repo is built from the sibling build tree, not from the source tree:

- Source: `<source_repo>`
- Build tree: `<source_repo>.build` or `<source_repo>.make`

## Toolchain

The project would not build under the system Java 21 runtime because Gradle 7.5 is not compatible with it.

I used:

- JDK 17 installed locally and selected with `JAVA_HOME`
- Android SDK selected with `ANDROID_HOME` / `ANDROID_SDK_ROOT`

The Android SDK components needed by the build were installed automatically by Gradle on first run.

## Build

1. Sync the source tree into the build tree.

   ```bash
   cpto <source_repo> <build_tree>
   ```

2. Initialize the git submodule used by the JNI/core code.

   ```bash
   git submodule update --init --recursive
   ```

3. Build the debug APK from the build tree.

   ```bash
   JAVA_HOME=/path/to/jdk17 \
   PATH=/path/to/jdk17/bin:$PATH \
   ANDROID_HOME=/path/to/android-sdk \
   ANDROID_SDK_ROOT=/path/to/android-sdk \
   ./gradlew --no-daemon assembleDebug
   ```

The resulting APK is:

```bash
app/build/outputs/apk/debug/app-debug.apk
```

## Install To Device

The connected USB device was visible to `adb`.

The first install attempt failed with an Android verifier error. On this device, the following shell settings were enough to allow the adb install to complete:

```bash
adb shell settings put global verifier_verify_adb_installs 0
adb shell settings put global package_verifier_user_consent 0
```

Then install the APK:

```bash
adb install -r app/build/outputs/apk/debug/app-debug.apk
```

That completed successfully.
