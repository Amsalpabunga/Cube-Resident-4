# CubeEvil4 Android rebuild package

This archive is the complete upstream `CubeEvil4-Android` source tree plus:
- `.github/workflows/build-core-signed.yml` for a GitHub Actions ARM32 + ARM64 Core APK build and APK signing.
- A Graphics-menu action to choose a PanVK ZIP from Android document storage.
- ZIP metadata and ELF validation, then copy the ARM64 PanVK ICD into app-private storage.

## Important PanVK limitation

**Importing the ZIP does not activate PanVK.** The imported `libvulkan_panfrost.so` is an ICD, not a standalone Vulkan loader. The existing game renderer calls Android's system `libvulkan.so`; Android does not expose a supported per-app API for selecting an arbitrary ICD from app-private storage. Calling the ICD's `vk_icdGetInstanceProcAddr` as if it were the loader's `vkGetInstanceProcAddr` is not a valid substitute.

This project therefore stages and validates the driver package, but continues to use the device's system Vulkan loader/driver. No claim is made that PanVK is running. A real activation implementation needs a compatible Vulkan loader/ICD integration and device testing; that is not present in this archive.

The target Realme C71 / Mali-G57 is also not guaranteed to be supported by this PanVK build. Its metadata targets Mali Bifrost/Valhall on the stock Arm kbase kernel driver, but compatibility must be tested on the actual device.

## Build and signing

1. Upload the **contents of this archive** to your GitHub repository (keep the root `.github` and `port` folders).
2. Open **Actions → Build signed CubeEvil4 Core APK → Run workflow**.
3. Download artifact `CubeEvil4-core-signed`.

The workflow builds both `armeabi-v7a` and `arm64-v8a`, then aligns and signs the APK.

### Persistent signing key (recommended)

Without secrets, the workflow generates a temporary key for that run. The APK is signed, but a later run will use a different key and cannot update-install over it.

To keep the same signing identity across runs, add these repository Actions secrets:
- `SIGNING_KEYSTORE_BASE64`: base64 of your JKS/keystore file
- `SIGNING_STORE_PASSWORD`
- `SIGNING_KEY_ALIAS`
- `SIGNING_KEY_PASSWORD`

Keep the keystore and passwords private. Do not commit them to the repository.

## Driver package import

In the app, open the in-game menu → Graphics → **Import PanVK driver ZIP** and select the PanVK package. The app checks `meta.json`, Android API minimum, library name, and AArch64 ELF header before copying the library into its private storage.

The app will explicitly report that the imported ICD is staged but not active.
