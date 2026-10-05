---
title: Bluetooth Commands
nav_order: 7
has_children: true
---

# Bluetooth Commands

[日本語](bluetooth-commands.md) | [English](bluetooth-commands_en.md)

The public command specification for communicating with SABERA glasses over Bluetooth.

## Precautions for direct transmission

{: .warning }
> Send Bluetooth commands directly at your own risk.
> Malfunctions, damage, or data loss caused by direct transmission are excluded from our warranty and support
> to the extent permitted by law. We assume no liability for such issues.

## Endpoints

| Purpose | Service UUID | Characteristic UUID |
|---|---|---|
| Send commands | `F48A23C0-F69A-11E8-8EB2-F2801F1B9FD1` | `F48A24C1-F69A-11E8-8EB2-F2801F1B9FD1` |
| Receive commands | `F48A23C0-F69A-11E8-8EB2-F2801F1B9FD1` | `F48A25C2-F69A-11E8-8EB2-F2801F1B9FD1` |
| Receive audio | `E49A3001-F69A-11E8-8EB2-F2801F1B9FD1` | `E49A3003-F69A-11E8-8EB2-F2801F1B9FD1` |

## Standard packet format

Byte sequences are written in hexadecimal and lengths are expressed in bytes. TLV stands for Type, Length, Value.

### Complete packet

| Header | Command ID | Type | Length | Value: payload |
|---|---|---|---|---|
| `01` | Command ID | `80` | Payload length (bytes) | Inner TLV 1 → inner TLV 2 → … |
| 1 byte | 1 byte | 1 byte | 2 bytes | Variable length |

### TLV inside the payload

| Type | Length | Value |
|---|---|---|
| Field type | Value length (bytes) | Value |
| 1 byte | 2 bytes | Variable length |

Length uses little-endian byte order for both the outer and inner TLVs. Strings use UTF-8. Notification timestamps use
8-byte big-endian values.

`LE(n)` represents integer `n` as a 2-byte little-endian value. `BE` means big-endian. Length is the byte count of the
Value. The outer Length is the byte count of all inner TLVs. In each command page, N is the byte count of the text or
compressed data.

### Example: open the home page

| Header | Command ID | Outer Type | Outer Length | Inner Type | Inner Length | Inner Value |
|---|---|---|---|---|---|---|
| `01` | `05` | `80` | `05 00` | `01` | `02 00` | `32 00` |
| Fixed | Page transition | Fixed | 5-byte payload | Page ID | 2-byte value | Home `0x0032` |

Pass the complete 10-byte packet, including the header, to `sendCommand()`:

```text
01 05 80 05 00 01 02 00 32 00
```

### Splitting text

For splittable text such as translation, teleprompter, and general text, prepend the following two bytes to the beginning
of the text TLV Value. Split only at UTF-8 character boundaries.

| Marker | Meaning |
|---|---|
| `5A 6B` | Complete in one packet |
| `5A 5A` | First packet of a split sequence |
| `7C 7C` | Middle packet of a split sequence |
| `6B 6B` | Last packet of a split sequence |

### Example: display text

Write the following packets in order to the command-send characteristic. The first opens the general text page and the
second displays `Hello`.

```text
01 05 80 05 00 01 02 00 47 00
01 16 80 0A 00 01 07 00 5A 6B 48 65 6C 6C 6F
```

The second packet consists of command `16`, outer length 10, text TLV type `01`, Value length 7, the single-packet marker
`5A 6B`, and UTF-8 `Hello`. When replacing the string, update both lengths to match the number of UTF-8 bytes.
General text is limited to 200 bytes per packet. For longer text, split it using the markers above, add the standard
header and text TLV to every packet, and send them in order. Send the next text only after the entire split sequence has
been sent.

## Commands sent to the glasses

| ID | Purpose | Main inner TLVs (Type) | Related API |
|---|---|---|---|
| [`0x01`](bluetooth-commands/tx-01.md) | Time synchronization | `01`: year, month, day, hour, minute, second, weekday | `syncTime()` |
| [`0x03`](bluetooth-commands/tx-03.md) | Notification | `01`: app name, `02`: subject, `03`: timestamp, `04`: body, `05`: count | `sendMessage()` / `syncNotificationCount()` |
| [`0x04`](bluetooth-commands/tx-04.md) | Teleprompter | `01`: body, `02`: line movement, `03`: progress, `04`: generating, `05`: status, `06`: time, `07`: mode | `sendTeleprompterContent()` / `sendTeleprompterLine()` / `sendTeleprompterStatus()` / `sendTeleprompterTime()` |
| [`0x05`](bluetooth-commands/tx-05.md) | Page transition | `01`: page ID | Page ID list |
| [`0x06`](bluetooth-commands/tx-06.md) | Settings and sync request | `01`: key, `02`: integer, `03`: Boolean, `04`: string, `05`: byte array, `80`: sync request | `sendSetting()` / `requestSettingSync()` |
| [`0x07`](bluetooth-commands/tx-07.md) | Translation | `01`: text, `02`: source language, `03`: target language | `sendTranslateContent()` / `sendTranslateLanguage()` |
| [`0x09`](bluetooth-commands/tx-09.md) | Microphone control | `01`: open (channel), `02`: close | `openGlassMic()` / `closeGlassMic()`; also used by `startMicStreaming()` / `stopMicStreaming()` |
| [`0x0A`](bluetooth-commands/tx-0a.md) | System status request | `01`: `00 00` | `requestSystemStatus()` |
| [`0x0B`](bluetooth-commands/tx-0b.md) | Weather synchronization | `01`: temperature, `02`: icon | `syncWeather()` |
| [`0x10`](bluetooth-commands/tx-10.md) | Navigation | `01`: status, `02`: icon, `03`–`06`: guidance text, `07`–`09`: small images, `0A`–`0C`: large images, `0D`: heading, `0E`: language | `sendNavi()` / `sendNaviStatus()` / `sendNaviCourse()` / `sendNaviLargeImage()` / `sendNaviLanguage()` |
| [`0x11`](bluetooth-commands/tx-11.md) | Display adjustment | `01`: status, `02`: image type | `sendAdjust()` |
| [`0x12`](bluetooth-commands/tx-12.md) | AI chat | `01`: text, `02`: sender, `03`: status, `04`: model, `05`: language, `06`: clear | `sendAiChatText()` / `sendAiChatSenderText()` / `sendAiChatStatus()` / `sendAiChatSenderStatus()` / `sendAiChatLanguage()` / `clearAiChat()` / `clearAiChatLegacy()` |
| [`0x13`](bluetooth-commands/tx-13.md) | General image | `01`: width, `02`: height, `03`: image data | `sendImage()` |
| [`0x14`](bluetooth-commands/tx-14.md) | Wake-up tilt threshold | `01`: angle | `sendWakeupTiltThreshold()` |
| [`0x15`](bluetooth-commands/tx-15.md) | Settings-page visibility | `01`: visibility state | `sendSettingPageVisibility()` |
| [`0x16`](bluetooth-commands/tx-16.md) | General text | `01`: text, `02`: status | `sendEmptyScreenContent()` (text) |
| [`0x17`](bluetooth-commands/tx-17.md) | Clear teleprompter and translation text | No payload | `clearInscriptionText()` |
| [`0x18`](bluetooth-commands/tx-18.md) | Debug phone name | `01`: name | `sendDebugPhoneName()` |
| [`0x19`](bluetooth-commands/tx-19.md) | IMU transmission control | `01`: start `01 00` / stop `00 00` | `startImuData()` / `stopImuData()` |
| [`0x1A`](bluetooth-commands/tx-1a.md) | Split layout | `01`: mode, `02`: region text | `sendLayout()` / `sendLayoutTexts()` / `closeLayout()` |
| [`0x1B`](bluetooth-commands/tx-1b.md) | Canvas | `01`: control, `02`: element, `03`: image, `04`: start animation, `05`: frame, `06`: stop | `sendCanvas()` / `sendCanvasElements()` / `sendCanvasImage()` / `removeCanvasImage()` / `clearCanvas()` / `closeCanvas()` / `startCanvasAnimation()` / `sendCanvasAnimationFrame()` / `stopCanvasAnimation()` |

## Commands received from the glasses

| ID | Purpose |
|---|---|
| [`0x80`](bluetooth-commands/rx-80.md) | General response |
| [`0x82`](bluetooth-commands/rx-82.md) | Keys and gestures |
| [`0x83`](bluetooth-commands/rx-83.md) | Settings synchronization response |
| [`0x87`](bluetooth-commands/rx-87.md) | System status response |
| [`0x88`](bluetooth-commands/rx-88.md) | Notification count response |
| [`0x89`](bluetooth-commands/rx-89.md) | IMU data |

Received packets also use the format `01` → command ID → `80` → Length (2 bytes, LE) → inner TLVs.

## Related

- [API history](api-history_en.md) — When APIs were added and which firmware versions they support
- [CommandManager](api/command-manager/) — APIs for creating and sending commands
- [sendCommand](api/glass-client/send-command.md) — Send one packet
- [sendCommandList](api/glass-client/send-command-list.md) — Send packets in order
- [Page guides](pages/index_en.md) — Flows from page transitions through content transmission
