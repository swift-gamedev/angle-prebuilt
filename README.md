# ANGLE Prebuilt

[![Build][build-badge]][build-workflow]

Prebuilt [ANGLE](https://chromium.googlesource.com/angle/angle) shared libraries for native EGL and OpenGL ES
programs, so they do not need depot_tools to get them. This is the
[Swaylib](https://github.com/swift-gamedev/Swaylib) fork of
[dsafdsaf132/angle-prebuilt](https://github.com/dsafdsaf132/angle-prebuilt); what it changes is listed below.

Chrome Stable releases are checked daily. Dev builds follow upstream ANGLE and run after the weekly Sunday 00:00 UTC
sync.

## Releases

Each release tag points at one ANGLE commit and is never moved. Published stable releases are never changed; a manual
dev build may replace the assets of a dev release from the same commit.

- Stable: `angle-stable-chrome-{chromeVersion}-{sha7}`, the ANGLE commit that Chrome Stable ships.
- Dev: `angle-dev-{date}-{sha7}`, a prerelease built from the fork's latest upstream sync.

`{sha7}` is the short upstream ANGLE commit. Each release has one archive per target and a `SHA256SUMS` file. Pin a
tag, download the archive and `SHA256SUMS`, and check it before extracting:

```sh
sha256sum --check --ignore-missing SHA256SUMS
```

## Targets

| Target | Archive | Runtime smoke test |
| --- | --- | --- |
| Linux x64 | `{tag}-linux-x64.tar.gz` | yes |
| Linux arm64 | `{tag}-linux-arm64.tar.gz` | yes |
| macOS x64 | `{tag}-darwin-x64.tar.gz` | no (skipped on the hosted Intel runner) |
| macOS arm64 | `{tag}-darwin-arm64.tar.gz` | yes |
| macOS universal | `{tag}-darwin-universal.tar.gz` | no (made from the two above) |
| Windows x64 | `{tag}-win32-x64.zip` | yes |
| Windows arm64 | `{tag}-win32-arm64.zip` | yes |

The smoke test creates a context on the platform's default ANGLE backend and on SwiftShader, and on Linux also draws to
an X11 window through SwiftShader under Xvfb.

## Archive layout

```text
angle/
  include/
    EGL/
    GLES/
    GLES2/
    GLES3/
    KHR/
  out/
    Release/
      libEGL.*
      libGLESv2.*
      libvk_swiftshader.* and vk_swiftshader_icd.json
      platform-specific import/runtime files
  LICENSE
  SWIFTSHADER_LICENSE.txt
  VULKAN_LOADER_LICENSE.txt (Windows)
  angle-build.json
```

`angle-build.json` records the ANGLE ref, resolved commit, platform, arch, Release/shared-library settings, generation
time, GN args and the release provenance.

## Changes from upstream

- Linux builds turn on SwiftShader's X11 (XCB) window surface (`use_ozone` with only `ozone_platform_x11`). Without
  it SwiftShader can only render offscreen, so an X11 program such as a GLFW window cannot use ANGLE's software
  renderer. CI checks that the library has the surface and draws to a window with it.
- Releases include a `SHA256SUMS` file covering every archive.
- Checkouts that only read the repository do not keep the token.

## License

ANGLE is distributed under the BSD-style license in `LICENSE`.

Redistributed archives include `LICENSE`. Review third-party and platform runtime redistribution terms for files
included by each build, especially `d3dcompiler_47.dll` on Windows.

[build-badge]: https://github.com/swift-gamedev/angle-prebuilt/actions/workflows/build.yml/badge.svg
[build-workflow]: https://github.com/swift-gamedev/angle-prebuilt/actions/workflows/build.yml
