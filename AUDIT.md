# Source audit of the upstream import

This fork is based on OpenEUICC commit
`cd09fb5963a16d845daa2e0f1f34001c712c1adb` (upstream mirror
`estkme-group/openeuicc`, 837 commits, plus the two pinned submodules).

## Scope

Static review of the tracked source tree and the build configuration:

- `LICENSE`, `README.md`, `NOTICE`, submodule licenses and `.gitmodules`.
- Android manifests: permissions, services, receivers, providers.
- Gradle/Kotlin build files: repositories, plugins, dependencies, toolchain.
- Kotlin/Java sources: process execution, dynamic loading, network endpoints.
- Repository contents: prebuilt binaries, embedded keys, obfuscation.
- Submodules pinned by commit: `lpac` and `cJSON`.

## Results

- No telemetry, analytics, crash-reporting or advertising SDKs
  (checked for Firebase, Crashlytics, Sentry, AppsFlyer, Mixpanel, Umeng,
  Bugsnag, Amplitude, Datadog).
- No prebuilt native libraries, DEX files or bundled APKs. The only binary
  artifact is the standard Gradle wrapper JAR.
- No hardcoded private keys, API tokens or credentials.
- No dynamic code loading from external paths; the only `System.loadLibrary`
  call loads `lpac-jni`, which is compiled from the pinned `lpac` submodule
  at build time.
- `Runtime.exec` usage is limited to `logcat` (bug-report helper) and
  `getprop` (compatibility probe).
- Dependencies come from Google Maven and Maven Central. One additional
  Maven repository is pinned to an immutable commit hash
  (`lineage-next/gradle-generatebp`). Submodules are pinned to specific
  commits.
- Declared permissions match an LPA (eSIM management) app:
  `INTERNET`, `READ_PHONE_STATE`, `WRITE_EMBEDDED_SUBSCRIPTIONS`,
  `MODIFY_PHONE_STATE`, `SECURE_ELEMENT_PRIVILEGED_OPERATION`, foreground
  service, notifications and camera (QR-code scanning). The privileged
  variant additionally relies on being installed as a system app.
- Network use is limited to what the user initiates: SM-DP+/SM-DS endpoints
  from activation codes or QR codes, plus documentation links in the UI.

## Limitations

This is a static source review, not a formal security audit, and it is not
proof that the software is free of defects or malware. It does not cover
dynamic/runtime behavior, the full dependency tree, or the firmware and
networks the app may talk to. Build artifacts in this repository were
produced from the audited source with a fork-controlled signing key.
