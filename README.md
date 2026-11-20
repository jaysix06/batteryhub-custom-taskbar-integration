# BatteryHub Integration to Custom Taskbar

A [Windhawk](https://windhawk.net/) mod that makes the **BatteryHub** mouse/keyboard
battery widget look like it belongs on a custom-styled Windows 11 taskbar.

```
Before:  [ ^ ][tray icons][clock][🖱 93% ⌨ 100%]   ← own dark box, covers the clock
After:   [🖱 93%  ⌨ 100%][ ^ ][tray icons][clock]  ← no box, aligned with the tray
```

## The problem

BatteryHub draws its battery widget as a child window of the taskbar and places it
to the left of the classic `TrayNotifyWnd` rectangle. Custom taskbar themes (such as
the **FluentGlass** theme of *Windows 11 Taskbar Styler*) move the visible tray icons
somewhere else, so the widget:

- lands after the clock and covers part of it,
- shows its own dark background box,
- jumps back to its original spot while you hover over it,
- disappears when an auto-hidden taskbar slides back in.

## What the mod does

| Fix | How |
| --- | --- |
| Widget sits just left of the tray "show hidden icons" arrow | Reads the real tray button position with UI Automation and moves the widget there |
| Vertically centred on the tray icons | Aligns the widget's centre with the tray arrow's centre |
| No background box (optional) | Hooks `UpdateLayeredWindow` and turns the box colour transparent ("colour to alpha"), keeping anti-aliased text and icons |
| Stays in place while hovering | Redirects BatteryHub's own `SetWindowPos` / `MoveWindow` / `UpdateLayeredWindow` position changes |
| Works with an auto-hiding taskbar | Positions relative to the taskbar and ignores stale positions reported mid-animation |

The mod is only injected into `BatteryHub.exe`. It does not touch Explorer.

## Compatibility

### Tested with

| Component | Version / setting |
| --- | --- |
| Windows 11 | 24H2 (build 26100) |
| BatteryHub | 1.1.0 |
| Windhawk | 1.7.3 |
| [Windows 11 Taskbar Styler](https://windhawk.net/mods/windows-11-taskbar-styler) | 1.11.2, **FluentGlass** theme |
| [Taskbar height and icon size](https://windhawk.net/mods/taskbar-icon-size) | 1.3.10 |
| [Taskbar Dock Animation](https://windhawk.net/mods/taskbar-dock-animation) | 1.9.2 |
| [Taskbar auto-hide when maximized](https://windhawk.net/mods/taskbar-auto-hide-when-maximized) | 1.2.6 |
| [Taskbar tray system icon tweaks](https://windhawk.net/mods/taskbar-tray-system-icon-tweaks) | 1.3 |

### Should also work

- Other Windows 11 Taskbar Styler themes, as long as the tray still uses the standard
  tray buttons (`SystemTray.NormalButton`).
- The default, unstyled Windows 11 taskbar.

Not supported: Windows 10 taskbar, ExplorerPatcher / StartAllBack classic taskbars
(they don't have the Windows 11 XAML tray). Reports for other setups are welcome in
[Issues](../../issues).

## Installation

1. Install [Windhawk](https://windhawk.net/).
2. In Windhawk, click **Create a new mod**.
3. Replace the editor contents with
   [`batteryhub-custom-taskbar-integration.wh.cpp`](batteryhub-custom-taskbar-integration.wh.cpp).
4. Click **Compile Mod**, then **Exit Editing Mode**.
5. Restart BatteryHub (or wait a moment) and the widget moves next to the tray arrow.

## Settings

| Setting | Default | Description |
| --- | --- | --- |
| Gap (px) | `4` | Space between the widget and the tray arrow |
| Extra horizontal offset (px) | `0` | Positive moves the widget right, negative moves it left |
| Check interval (ms) | `750` | How often the tray position is re-checked |
| Remove widget background | `true` | Turn off to keep BatteryHub's own box |

## Changelog

- **1.4**: Safe unloading (bounded UI Automation calls, no blocking cross-thread moves). Finds the tray arrow by its AutomationId, with a fallback when it's hidden. Positions per taskbar. Leaves BatteryHub's drawing untouched when there's no clear background box or no alpha channel.
- **1.3**: Widget follows an auto-hiding taskbar. Ignores stale tray positions while the taskbar animates.
- **1.2**: Stays in place while hovering. Vertically centred on the tray icons.
- **1.1**: Removes BatteryHub's background box.
- **1.0**: Places the widget left of the tray arrow.

## License

[MIT](LICENSE)

BatteryHub is a separate project by the BatteryHub contributors. This mod isn't affiliated with it.
