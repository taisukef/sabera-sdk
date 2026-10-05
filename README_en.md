# Sabera App SDK Samples

[日本語](README.md) | [English](README_en.md)

A collection of sample applications demonstrating how to use the Sabera App SDK.

## Samples

| Sample | Platform | Description |
|---|---|---|
| [Flutter](samples/flutter/) | Android / iOS | Use the KMP SDK from Dart through MethodChannel / EventChannel |
| [KMP](samples/kmp/) | Android / iOS | Use the SDK directly from Kotlin Multiplatform |

## Documentation

- [Getting Started](docs/getting-started_en.md) — Set up the SDK and learn the basics
- [Page guides](docs/pages/index_en.md) — How to open each glass page and what to send
- [API reference](docs/api/index_en.md) — List of public APIs (in progress)
- [Bluetooth commands](docs/bluetooth-commands_en.md) — Command IDs, packet formats, and related APIs
- [Third-party notices](docs/third-party-notices_en.md) — OSS included in the SDK and required application notices
- [Authoring documentation](docs/authoring_en.md) — Site structure, code-example validation, and deployment

## Prerequisites

The SDK is downloaded from GitHub Packages (`jig-SABERA/sabera-sdk-packages`). Because the package is private,
configure a PAT with the `read:packages` scope in `~/.gradle/gradle.properties`.

```properties
GitHubPackagesUsername=<your GitHub username>
GitHubPackagesPassword=<a PAT with read:packages>
```

See [Getting Started](docs/getting-started_en.md) for details.

iOS packages are downloaded with Swift Package Manager. In Xcode, add this repository's URL under Add Package Dependencies
and select version `1.1.0`. Choose the `SaberaAppSDK` product for the regular KMP SDK, or `SaberaIOS` for the full version
including BLE and Opus. The full version requires iOS 18.2 or later.

Applications using the full version should add `SaberaIOS` to their Swift Package dependencies and import:

```swift
import SaberaAppSDK
import SaberaIOSBridge
```

The XCFramework is hosted on GitHub Packages. Because **SPM cannot add an Authorization header**, credentials are required
in `~/.netrc`:

```
machine maven.pkg.github.com
  login <your GitHub username>
  password <a PAT with read:packages>
```

## License

The sample code in this repository is provided under the [Apache License 2.0](LICENSE). You may modify it freely and
incorporate it into your own application.

However, the **SDK itself (`jp.jig.sabera.app.sdk:*`) is not covered by this license**. The SDK is distributed as binaries
from GitHub Packages and is subject to separate SDK terms of use.

| Item | License |
|---|---|
| Samples and wrapper code in this repository | Apache License 2.0 |
| Sabera App SDK (AAR / XCFramework) | [SDK Terms of Use](https://github.com/jig-SABERA/sabera-sdk-packages/blob/main/TERMS.md) |
