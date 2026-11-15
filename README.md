# ESM Companion

A fork of **EasySkyMesh** by IoTThinks, a power-saving firmware with duty cycle and other tweaks.  The fork adds quality-of-life features for companion radios:

- **Auto-adverts**: send adverts automatically on a timer, nearby (zero-hop) and/or across the whole mesh (flood).
- **GPS privacy by zone**: your location is never shared while you're inside an area you choose, such as your home.
- **Boot advert**: one zero-hop advert shortly after the radio powers on.

Everything else, including PowerSaving, works as in EasySkyMesh.

## Tested hardware

Tested on the **RAK WisMesh Tag** (BLE companion) with the default PowerSaving, GPS and auto-advert all enabled.
With that setup you can expect **7–10 days of battery life**.

Firmware is built for every board EasySkyMesh builds companion firmware for, but only the WisMesh Tag has been tested.

## Download

Open the latest [Build Companion Firmwares](https://github.com/pmow/ESM-Companion/actions/workflows/build-companion-firmwares.yml) run,
and under **Artifacts** download the one named after your board, e.g. `RAK_WisMesh_Tag_companion_radio_ble`
(`.uf2` for drag-and-drop, `.zip` for DFU). You need to be signed in to GitHub to download artifacts.

## Using the Command Line

All settings are changed with text commands from the MeshCore app:

1. Connect the app to your radio over Bluetooth.
2. Open **Settings** and scroll to the bottom.
3. Tap **Command Line**.
4. Type a command (for example `gps` or `zone`) and send it. The radio's reply appears below it.

The same commands also work on the USB serial console (115200 baud).

### GPS

| Command | What it does |
|---|---|
| `gps` | Show GPS status: on/off, powered or sleeping, fix or no fix, satellites, when it next sleeps or wakes, and the last time sync |
| `gps on` / `gps off` | Turn the GPS on or off, same as the app UI |
| `gps sync` | Ask the GPS to get a fix and sync the time now |

With PowerSaving on, the GPS wakes up periodically and on telemetry requests, then sleeps again to save battery.

### GPS privacy zone

| Command | What it does |
|---|---|
| `zone box lat1 lon1 lat2 lon2` | Hide your location inside this rectangle, given as two opposite corners. Also turns the zone on. |
| `zone` | Show whether the zone is on, its corners, and whether you're inside it right now |
| `zone on` / `zone off` | Turn the zone on or off |
| `zone clear` | Erase the zone and turn it off |

Coordinates are decimal degrees, **latitude first**. For example, a box around a house:

```
zone box 52.3720 4.8890 52.3740 4.8920
```

In Google Maps, right-click (or long-press) a spot to copy its coordinates. Make the box a little bigger
than you need, because GPS readings can drift by tens of metres. Run `zone` at home to check it says
`inside: location hidden`.

Zone commands can't be used over remote CLI (LoRa), so only you can read or change your zone.

### Auto-adverts

| Command | What it does |
|---|---|
| `set advert.interval minutes` | Zero-hop advert to nearby nodes every 60–240 minutes, `0` = off |
| `set flood.advert.interval hours` | Advert flooded across the mesh every 3–168 hours, `0` = off |
| `get advert.interval` / `get flood.advert.interval` | Show the current setting |

Both are off by default. One zero-hop advert is also sent 15 seconds after every boot.
Every advert follows the app's location-sharing setting and the GPS privacy zone.

### PowerSaving and other

| Command | What it does |
|---|---|
| `powersaving` | Show whether PowerSaving is on |
| `powersaving on` / `powersaving off` | Turn PowerSaving on or off (on by default) |
| `ver` | Show the firmware version and build date |

## Building

```bash
FIRMWARE_VERSION=gpszone bash build.sh build-firmware RAK_WisMesh_Tag_companion_radio_ble
```

`build.sh` turns the GPS privacy zone on for every companion target.

---

Based on EasySkyMesh by IoTThinks: **https://github.com/IoTThinks/EasySkyMesh**
