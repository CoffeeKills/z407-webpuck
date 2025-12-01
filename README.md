# Logitech Z407 Web Puck Controller

A web-based replacement for the Logitech Z407 wireless control dial (puck). This tool uses the Web Bluetooth API to directly control the speakers, offering a solution when the physical dial is broken or lost.

## Features

- **Volume & Bass Control**: Adjust volume and bass levels.
- **Playback Control**: Play/Pause, Next Track, Previous Track.
- **Source Switching**: Switch between Bluetooth, AUX, and USB inputs.
- **Advanced Controls**: Send custom hex commands and perform factory resets.
- **No Installation Required**: Runs entirely in the browser.

## Requirements

- A browser with **Web Bluetooth** support:
  - Google Chrome (Desktop & Android)
  - Microsoft Edge
  - Opera
  - *Note: Firefox and Safari do not currently support Web Bluetooth.*
- Bluetooth hardware on your device.

## Usage

1. Open the [hosted page](https://<your-username>.github.io/z407-webpuck/) (or `index.html` locally).
   - *Alternative*: Try the [Neuromorphic Design](neuromorphic.html) for a modern look.
2. Click **"1. Connect to Speaker"**.
3. Select your **Logitech Z407** from the device list.
4. Once connected, the controls will appear.

## Documentation

- [Protocol Documentation](PROTOCOL.md) - Technical details on the reverse-engineered BLE protocol.

## Troubleshooting

- **"Web Bluetooth API is not available"**: Ensure you are using a supported browser (Chrome/Edge).
- **Device not found**: Make sure the speakers are plugged in and within range.
- **Connection fails**: Try refreshing the page or power-cycling the speakers.

## Disclaimer

This is an unofficial tool and is not affiliated with Logitech. Use at your own risk.
