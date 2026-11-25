# Carrom Master AI

Two integrated but strictly separated modules in one Android app (package `com.carrommaster.ai`).

| Package | Role | May depend on |
|---|---|---|
| `core` | Shared pure-Kotlin models, board spec, planner contract | nothing |
| `analyzer` | Module A: import screenshots, OpenCV board/coin detection (offline, read-only) | `core` |
| `simulator` | Module B: LibGDX + Box2D game, AI player, runs only inside its own world | `core` |
| `data` | Room (match history) | `core` |
| `ui` | Compose screens + navigation | all |

`ArchitectureBoundaryTest` fails the build if analyzer/simulator import each other, or if any
accessibility / screen-capture / input-injection API or overlay permission appears. The app has no
INTERNET permission. Module A only analyses images the user picks; it never sends input to Carrom Pool.

## Build
1. Android Studio Koala+ (JDK 17), open this folder, let Gradle sync (first sync downloads LibGDX natives into `app/libs/`).
2. Unit tests: see docs/PHASE1_VERIFICATION.md (the Gradle wrapper scripts/jar are NOT included; Android Studio does not need them, the command line does).
3. Run on device/emulator: Home -> Module B -> Test shot / Reset. Module A -> Import screenshot (shows OpenCV version).

## Roadmap
1. Foundation (this phase)  2. Module A board detection  3. Module A coin recognition + coordinates
4. Shared trajectory/physics predictor + ShotPlanner  5. Module A recommendations/overlay
6. Module B rules, turns, scoring  7. Module B AI player loop (AI vs AI, Human vs AI)  8. Polish/performance

## Build the APK without Android Studio
Push this folder to a GitHub repository, open Actions > "Build APK" > Run workflow.
When it finishes, download the `CarromMasterAI-debug-apk` artifact (app-debug.apk) and install it.
