# Hyprland Multi-Monitor Brightness Control

Control external monitor brightness using keyboard brightness keys, with the monitor under the mouse cursor.

## Features

- Adjusts brightness based on **mouse position** (not focused workspace)
- Uses DDC/CI to control external monitors via `ddcutil`
- Uses `brightnessctl` for internal/built-in displays
- Auto-detects monitors on hotplug
- Fast cached DDC bus mapping

## Dependencies

- `hyprland`
- `ddcutil` - for external monitor control
- `brightnessctl` - for internal display control
- `jq` - for JSON parsing
- `swayosd-client` - for on-screen display (optional, can be removed)

## Installation

1. Copy all scripts to `~/.config/hypr/scripts/brightness/`:
   ```sh
   mkdir -p ~/.config/hypr/scripts/brightness
   cp hypr-brightness hypr-ddc-update hypr-monitor-hook ~/.config/hypr/scripts/brightness/
   chmod +x ~/.config/hypr/scripts/brightness/*
   ```

2. Add to your Hyprland autostart (`~/.config/hypr/autostart.conf`):
   ```
   exec-once = ~/.config/hypr/scripts/brightness/hypr-ddc-update
   exec-once = ~/.config/hypr/scripts/brightness/hypr-monitor-hook
   ```

3. Update your brightness key bindings (`~/.config/hypr/bindings.conf`):
   ```
   unbind = , XF86MonBrightnessDown
   unbind = , XF86MonBrightnessUp
   binde = , XF86MonBrightnessDown, exec, ~/.config/hypr/scripts/brightness/hypr-brightness -
   binde = , XF86MonBrightnessUp, exec, ~/.config/hypr/scripts/brightness/hypr-brightness +
   ```

4. Restart Hyprland or log out/in.

## Usage

Press the brightness keys (e.g., `XF86MonBrightnessUp` / `XF86MonBrightnessDown`) while your cursor is on the monitor you want to adjust.

- **Internal display (eDP-1)**: Uses `brightnessctl`
- **External monitors**: Uses DDC/CI via `ddcutil`

## How It Works

1. `hypr-brightness` detects which monitor the cursor is on using cursor coordinates
2. For internal displays, it uses `brightnessctl`
3. For external displays, it looks up the DDC bus ID from `ddc_bus_map.txt`
4. If the cache is missing/outdated, it runs `hypr-ddc-update` to regenerate it
5. `hypr-monitor-hook` listens for monitor connect/disconnect events and updates the cache automatically
