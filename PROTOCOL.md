# Controlling the Logitech Z407 Speakers via BLE

## Overview

This document outlines the reverse-engineered Bluetooth Low Energy (BLE) protocol used by the Logitech Z407 wireless control puck. By understanding this protocol, it's possible to create custom applications and scripts to control speaker functions like volume, bass, source switching, and more from a computer.

**Note:** This protocol *emulates* the commands sent by the physical puck. The commands work regardless of whether the active source is Bluetooth, AUX, or USB.

The communication relies on writing specific byte arrays to one BLE "characteristic" and listening for response notifications on another. A critical "handshake" sequence is required upon connection to establish a stable link.

## BLE Connection Details

To communicate with the speaker, you must connect to a specific service and interact with its two characteristics.

- **Service UUID**: `0000fdc2-0000-1000-8000-00805f9b34fb`
  - This is the primary service that exposes all the control functionality.
- **Command Characteristic UUID**: `c2e758b9-0e78-41e0-b0cb-98a593193fc5`
  - This characteristic is writable. You send commands to the speaker by writing byte arrays to it.
- **Response Characteristic UUID**: `b84ac9c6-29c5-46d4-bba1-9d534784330f`
  - This characteristic is notifiable. You must subscribe to it to receive confirmation and status update messages from the speaker.

## The Communication Protocol

A stable connection requires a specific sequence of events. Sending user commands without performing the initial handshake will cause the speaker to ignore the command or disconnect the client.

### Step 1: Scan and Connect

1. Scan for BLE devices advertising the Service UUID listed above.
2. Connect to the discovered device.

### Step 2: Subscribe to Notifications

Immediately after connecting, you must subscribe to notifications on the Response Characteristic. All feedback from the speaker, including the critical handshake steps, is sent via these notifications.

### Step 3: Perform the Handshake (State Machine)

The handshake authenticates the client to the speaker. It follows a specific Challenge-Response pattern:

1.  **Initiate:** The client sends the `INITIATE` command (`0x84 0x05`).
2.  **Challenge:** The speaker responds with a challenge notification (`d40501`).
3.  **Acknowledge:** Upon receiving the challenge, the client *must* send the `ACKNOWLEDGE` command (`0x84 0x00`).
4.  **Secured:** The speaker confirms the connection with a final response (`d40003` for bonded or `d40001` for standard). The link is now stable and ready for commands.

### Step 4: Send Commands and Receive Responses

After the handshake is complete, you can now send any of the user control commands. For every command you send, the speaker will typically reply with a corresponding notification on the Response Characteristic to confirm the action was received.

## Implementation Notes & Behavioral Quirks

- **Audio Controls (`VOLUME_UP`/`VOLUME_DOWN`)**: This behavior has been observed on both Bluetooth and USB sources. The speaker appears to enter a low-power or sleep state when no audio is playing. Consequently, `VOLUME_UP` and `VOLUME_DOWN` commands may only function correctly when an audio stream is actively playing. For example, you cannot "pre-adjust" the volume down before starting music; the commands will be ignored until playback begins.
- **Playback Controls (`PLAY_PAUSE`, `NEXT_TRACK`, `PREV_TRACK`)**: These commands are passed through to the connected host device (e.g., a computer or phone) and function as standard media key presses. Their reliability and effect depend entirely on the host operating system's "dominant" media player (e.g., Spotify, a browser tab) and can be inconsistent.

## Command Reference

### Handshake Commands

| Command Name  | Hex Value    | Decimal Value |
| :------------ | :----------- | :------------ |
| `INITIATE`    | `0x84 0x05`  | `132 5`       |
| `ACKNOWLEDGE` | `0x84 0x00`  | `132 0`       |

### User Control Commands

| Command Name         | Hex Value    | Decimal Value | Description                                   |
| :------------------- | :----------- | :------------ | :-------------------------------------------- |
| **Audio**            |              |               |                                               |
| `VOLUME_UP`          | `0x80 0x02`  | `128 2`       | Increases the master volume.                  |
| `VOLUME_DOWN`        | `0x80 0x03`  | `128 3`       | Decreases the master volume.                  |
| `BASS_UP`            | `0x80 0x00`  | `128 0`       | Increases the bass level.                     |
| `BASS_DOWN`          | `0x80 0x01`  | `128 1`       | Decreases the bass level.                     |
| **Playback**         |              |               |                                               |
| `PLAY_PAUSE`         | `0x80 0x04`  | `128 4`       | Toggles play/pause for the Bluetooth source.  |
| `NEXT_TRACK`         | `0x80 0x05`  | `128 5`       | Skips to the next track.                      |
| `PREV_TRACK`         | `0x80 0x06`  | `128 6`       | Goes to the previous track.                   |
| **Source Switching** |              |               |                                               |
| `SWITCH_BLUETOOTH`   | `0x81 0x01`  | `129 1`       | Switches the active input to Bluetooth.       |
| `SWITCH_AUX`         | `0x81 0x02`  | `129 2`       | Switches the active input to the 3.5mm AUX jack. |
| `SWITCH_USB`         | `0x81 0x03`  | `129 3`       | Switches the active input to USB.             |
| **Auditory Cues**    |              |               |                                               |
| `SOUND_1`            | `0x85 0x01`  | `133 1`       | Plays Sound 1 (failure/disconnect chime).     |
| `SOUND_2`            | `0x85 0x02`  | `133 2`       | Plays Sound 2 (mode switch chime).            |
| `SOUND_3`            | `0x85 0x03`  | `133 3`       | Plays Sound 3 (connection chime).             |
| **System**           |              |               |                                               |
| `PAIRING`            | `0x82 0x00`  | `130 0`       | Puts the speaker into Bluetooth pairing mode. |
| `FACTORY_RESET`      | `0x83 0x00`  | `131 0`       | Resets the speaker to factory defaults.       |

### Unknown Commands
| Command Name         | Hex Value    | Decimal Value | Description                                   |
| :------------------- | :----------- | :------------ | :-------------------------------------------- |
| `UNKNOWN_1`            | `0x85 0x00`  | `133 0`       | Has no noticeable effect. |

## Response Reference

| Response Name          | Hex String | Triggering Action(s)                                                  |
| :--------------------- | :--------- | :-------------------------------------------------------------------- |
| **Handshake**          |            |                                                                       |
| `INITIATE_RESPONSE`    | `d40501`   | Sent in response to the client's `INITIATE` command.                  |
| `ACKNOWLEDGE_RESPONSE` | `d40001`   | Sent in response to the client's `ACKNOWLEDGE` command.               |
| `CONNECTED`            | `d40003`   | Sent after `d40001` to signal the connection is fully established.    |
| **Confirmations**      |            |                                                                       |
| `BASS_UP`              | `c000`     | `BASS_UP`                                                             |
| `BASS_DOWN`            | `c001`     | `BASS_DOWN`                                                           |
| `VOLUME_UP`            | `c002`     | `VOLUME_UP`                                                           |
| `VOLUME_DOWN`          | `c003`     | `VOLUME_DOWN`                                                         |
| `PLAY_PAUSE`           | `c004`     | `PLAY_PAUSE`                                                          |
| `NEXT_TRACK`           | `c005`     | `NEXT_TRACK`                                                          |
| `PREV_TRACK`           | `c006`     | `PREV_TRACK`                                                          |
| `SWITCH_BLUETOOTH`     | `c101`     | `SWITCH_BLUETOOTH`                                                    |
| `SWITCH_AUX`           | `c102`     | `SWITCH_AUX`                                                          |
| `SWITCH_USB`           | `c103`     | `SWITCH_USB`                                                          |
| `PAIRING`              | `c200`     | `PAIRING`                                                             |
| `FACTORY_RESET`        | `c300`     | `FACTORY_RESET`                                                       |
| `SOUND_1`              | `c503`     | `SOUND_1`                                                             |
| `SOUND_2`              | `c502`     | `SOUND_2`                                                             |
| `SOUND_3`              | `c501`     | `SOUND_3`                                                             |
| `UNKNOWN_1`            | `c500`     | `UNKNOWN_1` (`0x85 0x00`)                                             |
| **Status Updates**     |            |                                                                       |
| **Note:**              |            | These are sent *only if* the source actually changes.                 |
| `SWITCHED_BLE`         | `cf04`     | Sent after switching to Bluetooth is complete.                        |
| `SWITCHED_AUX`         | `cf05`     | Sent after switching to AUX is complete.                              |
| `SWITCHED_USB`         | `cf06`     | Sent after switching to USB is complete.                              |
