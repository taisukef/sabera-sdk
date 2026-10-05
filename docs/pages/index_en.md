---
title: Page Guides
nav_order: 4
has_children: true
---

# Page Guides

[日本語](index.md) | [English](index_en.md)

Each page on the glasses has a defined set of commands. This section summarizes how to open each page, what to send,
and how to clean up. It assumes that the device is already connected and a `CommandManager` has been created.
See [Getting Started](../getting-started_en.md) for the connection procedure.

```kotlin
val commandManager = client.createCommandManager()
```

## Page list

| Page | How to open | Main content | Required firmware |
|---|---|---|---|
| [Teleprompter](teleprompter.md) | `enterTeleprompterPage()` | Script, playback state, elapsed time | — |
| [Translation](translate.md) | `enterTranslatePage()` | Language pair and translation | — |
| [AI assistant](ai-chat.md) | `enterAiChatPage()` | Bubble text and generation state | — |
| [General text display](text.md) | `enterEmptyScreenPage()` | Text | — |
| [Image display](image.md) | `enterImageDisplayPage()` | Images up to 196x196 | — |
| [Split layout](layout.md) | `sendLayout()` | Split regions and text for each region | 1.2.0 |
| [Free-layout canvas](canvas.md) | `sendCanvas()` | Text and images at specified coordinates | 1.2.0 |
| [Navigation](navigation.md) | `enterNavigationPage()` | Guidance, direction, and map images | — |
| [Adjustment and debugging](adjust.md) | `enterGlassAngleAdjustmentPage()` and others | Tilt threshold and adjustment images | — |

“Required firmware” is the firmware version. On older firmware, commands are silently discarded, so the application may
appear to send successfully while nothing happens on the glasses.

## Common rules

**Open the page before sending content.** Most pages do not display content until they are open. Split layout and canvas
are exceptions: sending their content also switches the page.

**Sending is fire-and-forget.** Send methods are synchronous, but only enqueue the command internally and do not block the
calling thread. There is no response indicating whether the glasses received the command.

**Commands are discarded while disconnected.** Check `CommandManager.connected` before sending.

**Return to the home page when finished.** `enterHomePage()` closes the current page and discards its displayed content.

```kotlin
// Close the open page and discard its content.
commandManager.enterHomePage()
```

**There is a per-packet limit.** Script and body-text commands support splitting long content across packets. Text in split
layouts and canvases cannot be split and is truncated at roughly 190 bytes in total.
