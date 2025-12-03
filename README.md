# Logitech Z407 Web Puck Controller

A web-based replacement for the Logitech Z407 wireless control dial (puck). This tool uses the Web Bluetooth API to directly control the speakers, offering a solution when the physical dial is broken or lost.

## Features

- **Volume & Bass Control**: Adjust volume and bass levels.
- **Playback Control**: Play/Pause, Next Track, Previous Track.
- **Source Switching**: Switch between Bluetooth, AUX, and USB inputs.
- **pairing mode**: Put speakers into pairing mode.
- **Advanced Controls**: Send custom hex commands and perform factory resets.
- **No Installation Required**: Runs entirely in the browser.

## Requirements

- A browser with **Web Bluetooth** support:
  - Google Chrome (Desktop)
  - Microsoft Edge (Desktop)
  - Opera (Desktop)
  - *Note: Firefox and Safari do not currently support Web Bluetooth.*
- Bluetooth hardware on your device.

## Usage

1. Open the [hosted page](https://coffeekills.github.io/z407-webpuck/) (or `index.html` locally).
   - *Alternative*: Try the [Neuromorphic Design](https://coffeekills.github.io/z407-webpuck/neuromorphic.html) for a modern look.
2. Click **"1. Connect to Speaker"**.
3. Select your **Logitech Z407** from the device list.
4. Once connected, the controls will appear.

## Documentation

- [Protocol Documentation](PROTOCOL.md) - Technical details on the reverse-engineered BLE protocol.

## Troubleshooting

- **"Web Bluetooth API is not available"**: Ensure you are using a supported browser (Chrome/Edge).
- **Device not found**: Make sure the speakers are plugged in and within range.
- **Connection fails**: Try refreshing the page or power-cycling the speakers.

## Credits & Acknowledgements
- **AI Generated:** This project was built almost entirely with the assistance of AI.
- **Inspiration:** Early inspiration taken from [freundTech/logi-z407-reverse-engineering](https://github.com/freundTech/logi-z407-reverse-engineering).

## Disclaimer

This is an unofficial tool and is not affiliated with Logitech. Use at your own risk.
