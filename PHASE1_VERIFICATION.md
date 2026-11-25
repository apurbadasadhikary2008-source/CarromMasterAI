# Phase 1 verification report

STATUS: NOT BUILT. No Gradle build has been run. Nothing here claims the project compiles.

## A. Verified in the sandbox (scripted/static, no Android SDK)
- 25 Kotlin files: package declaration matches directory for every file.
- Every `com.carrommaster.ai.*` import resolves to a class declared in the project (0 unresolved).
- Brace/paren/bracket balance OK in all files.
- All 4 XML files well-formed; manifest refs (@string/app_name, @style/Theme.CarromMasterAI) and
  Kotlin refs (R.id.gdx_container x3) exist in res/values.
- gradle/libs.versions.toml parses; every `libs.*` / `libs.plugins.*` / `libs.versions.*` accessor used in
  the build files exists; every version.ref exists; no unused catalog entries.
- Starting layout: 19 coins (9/9/1), min centre distance 0.3244 >= 2r = 0.318 (no overlaps) - computed with
  an equivalent Python script, NOT by running the Kotlin test.
- Module boundary: no analyzer<->simulator imports, core imports neither; no AccessibilityService,
  dispatchGesture, injectInputEvent, MediaProjection, SYSTEM_ALERT_WINDOW, UiAutomation anywhere (grep).
- Manual line-by-line review of all Kotlin against the LibGDX/Box2D/Compose/Room APIs: no compile error found.

## B. Requires Android Studio / a real Gradle run (UNVERIFIED)
1. Gradle sync resolves all artifacts (needs network): AGP 8.5.2, Kotlin 2.0.20, KSP 2.0.20-1.0.25,
   Compose BOM 2024.09.00, Room 2.6.1, LibGDX 1.13.1 (+ natives classifier jars), OpenCV 4.9.0.
2. Kotlin compilation of all 25 files (external API signatures were checked by memory, not by compiler):
   OpenCVLoader.initLocal(), Core.VERSION, AndroidFragmentApplication.initializeForView(), Box2D bodies/fixtures.
3. KSP/Room annotation processing.
4. `copyAndroidNatives` Gradle task unpacking .so files to app/libs/<abi> and hooking *JniLibFolders tasks.
   Not compatible with Gradle configuration cache (uses project.copy at execution time) - leave it off.
5. Unit tests: StartingLayoutTest, ArchitectureBoundaryTest (expects working dir = app/, which Gradle uses).
6. On-device: LibGDX fragment inside Compose (Test shot / Reset), OpenCV loading, photo picker import.

## C. Known gaps
- Gradle wrapper (gradlew, gradlew.bat, gradle-wrapper.jar) not included.
- No launcher icon (default icon is used).
- Physics constants (damping 1.3, restitution 0.8, max speed 30 u/s) are untuned estimates.

## D. Commands
Android Studio (Koala 2024.1.1+, JDK 17 = Settings > Build Tools > Gradle > Gradle JDK):
1. File > Open > select CarromMasterAI folder. Wait for sync. (Studio writes local.properties for the SDK.)
2. Gradle tool window > CarromMasterAI > app > Tasks > verification > testDebugUnitTest (double-click).
   Or right-click app/src/test/java > Run 'Tests in ...'.
3. Build > Make Project, then Run 'app' on a device/emulator (API 26+).

Command line (needs a Gradle install or copied wrapper):
- One time: `gradle wrapper --gradle-version 8.9`   (or copy gradlew, gradlew.bat, gradle/wrapper/gradle-wrapper.jar
  from any new Android Studio project)
- `./gradlew :app:testDebugUnitTest`
- `./gradlew :app:assembleDebug`      -> app/build/outputs/apk/debug/app-debug.apk
- Windows: use `gradlew.bat` instead of `./gradlew`.

Report back the first error text if sync, compile or tests fail.
