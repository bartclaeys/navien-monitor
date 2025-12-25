# Navien Monitor Walkthrough

## Overview
A custom ESPHome firmware for M5Stack Basic (Core) to monitor and control a Navien Water Heater.
**Critical Mechanism**: The M5Stack interacts with the boiler **indirectly via a Home Assistant-controlled Smart Plug**.
- **Monitoring**: Reads power usage (Watts) from the Smart Plug to detect boiler activity.
- **Control**: "Reboot" function toggles the Smart Plug off/on to reset the boiler.

**Repository**: [github.com/bartclaeys/navien-monitor](https://github.com/bartclaeys/navien-monitor)

## Key Features

### 1. Visual Interface
- **Layout**: High-contrast, large text (**Roboto 32px**) for readability.
- **Theme**: 
    - **Normal**: Black Background / White Text.
    - **Alert**: **RED Background** / White Text (Triggered when Smart Plug Power < 5.0W).
- **Colors**:
    - **Main Data**: Grey (96, 96, 96).
    - **Meta Info**: **Dim Grey** (60, 60, 60) for Clock, Footer, and Labels.
- **Clock**: Blinking Colon (hh:mm <-> hh mm).
- **Format**: Temperature in **Fahrenheit (F)**.
- **Footer**: Compact 16px labels in **UPPERCASE** (Roboto Regular).
    - **BRIGHTNESS**: Flush Left.
    - **REBOOT NAVIEN**: Flush Right.

### 2. Controls & Audio
| Button | Label | Action | Audio Feedback |
| :--- | :--- | :--- | :--- |
| **A (Left)** | **BRIGHTNESS** | Cycles Backlight (100% -> 30% -> Off) | Soft Beep (1.25%) |
| **B (Mid)** | **ALERT** | **Simulates Alert** (Chirp + Red Flash) | **Quiet Chirp (5%)** |
| **C (Right)** | **REBOOT NAVIEN** | **Toggles Smart Plug** (Off -> 5s -> On) | Soft Beep |

### 3. Logic
- **Active Alert System**:
    - **Trigger**: Smart Plug Power < 5.0W (Boiler inactive/fault).
    - **Action**: Plays **Quiet Chirp** automatically + Displays Red Background.
- **Manual Test**: Pressing Center Button ("ALERT") triggers the same effect.
    - **Flash Logic**: Forces screen update (Red) -> Holds 500ms -> Forces update (Black).
- **Reboot Logic**: Button C triggers `script.reboot_navien`, which calls the switch entity for the Smart Plug.

## Optimizations (Technical)
- **Centralized Config**: All thresholds and levels defined in `substitutions`.
- **Memory Efficient**:
    - **8-Bit Color**: Uses RGB332 palette to save RAM/FPS.
    - **Font Glyphs**: Fonts restricted to only used characters (Numbers, Time, Uppercase A-Z, specific symbols) to save Flash storage.
    - **Clean Code**: Refactored display logic for efficiency.

## Environment Note
- **Python**: 3.13 (Manually installed)
- **ESPHome Version**: 2025.12.2 (Latest Stable).
- **Compiling**: Uses `.venv-3.13` for build environment.

## Deployment
- **Firmware**: `basic-esp32.yaml`
- **Method**: OTA (Over-The-Air) or USB.
- **Command**: 
  ```bash
  source .venv-3.13/bin/activate
  esphome run basic-esp32.yaml --device navien-monitor.local
  ```

## Files
- [basic-esp32.yaml](file:///Users/bartclaeys/.gemini/antigravity/scratch/esp32_work/basic-esp32.yaml): Main Configuration.
- [secrets.yaml](file:///Users/bartclaeys/.gemini/antigravity/scratch/esp32_work/secrets.yaml): WiFi/API credentials.
