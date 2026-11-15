# EasySkyMesh GPS Zone

A fork of **EasySkyMesh** (PowerSaving 18 by IoTThinks) that adds quality-of-life features for companion radios:

- **Auto-adverts**: send adverts automatically on a timer, nearby (zero-hop) and/or across the whole mesh (flood).
- **GPS privacy by zone**: your location is never shared while you're inside an area you choose, such as your home.
- **Boot advert**: one zero-hop advert shortly after the radio powers on.

Everything else, including PowerSaving, works as in EasySkyMesh.

## Tested hardware

Tested on the **RAK WisMesh Tag** (BLE companion) with the default PowerSaving, GPS and auto-advert all enabled.
With that setup you can expect **7–10 days of battery life**.

Firmware is built for every board EasySkyMesh builds companion firmware for, but only the WisMesh Tag has been tested.

## Download

Open the latest [Build Companion Firmwares](https://github.com/pmow/EasySkyMesh-GPSZone/actions/workflows/build-companion-firmwares.yml) run,
and under **Artifacts** download the one named after your board, e.g. `RAK_WisMesh_Tag_companion_radio_ble`
(`.uf2` for drag-and-drop, `.zip` for DFU). You need to be signed in to GitHub to download artifacts.

## Usage

Type these in the MeshCore app's **Command Line** screen (bottom of Settings) or on the USB serial console.

### GPS privacy zone

| Command | Meaning |
|---|---|
| `zone box <lat1> <lon1> <lat2> <lon2>` | Hide your location inside this rectangle (two opposite corners); turns the zone on |
| `zone` | Show the zone and whether you're inside it right now |
| `zone on` / `zone off` / `zone clear` | Turn on, turn off, erase |
| `zone1` … `zone4 <lat> <lon>` or `here` | Set corners one at a time, for a slanted shape |

Coordinates are decimal degrees, latitude first, e.g. `zone box 52.3720 4.8890 52.3740 4.8920`.
Zone commands can't be used over remote CLI (LoRa), so only you can read or change your zone.

### Auto-adverts

| Command | Range |
|---|---|
| `set advert.interval <minutes>` | 60–240, `0` = off (zero-hop) |
| `set flood.advert.interval <hours>` | 3–168, `0` = off (flood) |
| `get advert.interval` / `get flood.advert.interval` | Show the setting |

Every advert, including the boot advert, follows the app's location-sharing setting and the GPS privacy zone.

## Building

```bash
FIRMWARE_VERSION=gpszone bash build.sh build-firmware RAK_WisMesh_Tag_companion_radio_ble
```

`build.sh` turns the GPS privacy zone on for every companion target.

---

Based on EasySkyMesh by IoTThinks: **https://github.com/IoTThinks/EasySkyMesh**
