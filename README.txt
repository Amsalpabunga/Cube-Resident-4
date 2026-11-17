UPLOAD BUNDLE

Upload these items to the ROOT of your GitHub repository:
1. CubeEvil4-Android-master.zip
2. .github/workflows/build-core-signed.yml
3. PANVK-INTEGRATION-STATUS.md (optional documentation)

Then open Actions -> Build signed CubeEvil4 Core APK -> Run workflow.
The workflow extracts the full source archive, builds ARM32 + ARM64, signs the APK,
and publishes CubeEvil4-core-signed as an artifact.

IMPORTANT: The app currently validates and stages the PanVK ZIP in private storage.
It does NOT activate PanVK. The native app still uses Android's system Vulkan loader.
See PANVK-INTEGRATION-STATUS.md for the exact limitation.
