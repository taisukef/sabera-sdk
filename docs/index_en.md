---
title: Home
nav_order: 1
---

# Sabera App SDK

[日本語](index.md) | [English](index_en.md)

An SDK for building applications that communicate with SABERA glasses. The same API is available on Android and iOS.

## SABERA glass specifications

Hardware details relevant to application development.

| Item | Value |
|---|---|
| Display | 640x480, monocular right-eye display |
| Communication | Bluetooth Low Energy 5.3 |
| Microphones | 2; PCM16 / 16 kHz mono |
| Touch sensor | 1; tap, double-tap, and long press |
| IMU | 6DoF |
| Camera / speakers | None |

## What you can do

- Send text to built-in pages such as the teleprompter, translation, AI assistant, and navigation pages
- Place text and images at specified coordinates on a free-layout canvas
- Receive touch events, microphone audio, and IMU data for processing in the application

## Documentation

- [Getting Started](getting-started_en.md) — Setup and basic usage
- [Creating a GitHub PAT](github-pat_en.md) — Create the token required to download the SDK
- [Page guides](pages/index_en.md) — Calling flows for each page available on the glasses
- [API reference](api/index_en.md) — List of public APIs
- [Bluetooth commands](bluetooth-commands_en.md) — Command IDs, packet formats, and related APIs
- [Changelog](api-history_en.md) — Reference and SDK changes
- [Third-party notices](third-party-notices_en.md) — Third-party software used by the SDK

## Samples

| Sample | Platform |
|---|---|
| [Flutter](https://github.com/jig-SABERA/sabera-sdk/tree/main/samples/flutter) | Android |
| [KMP](https://github.com/jig-SABERA/sabera-sdk/tree/main/samples/kmp) | Android / iOS |
