# EasySkyMesh PowerSaving + GPS Privacy Zone

An unofficial build of **[EasySkyMesh](https://github.com/IoTThinks/EasySkyMesh)** PowerSaving 18 by
[IoTThinks](https://iotthinks.com/), the long-range, low-power MeshCore firmware, with a few additions
for **companion** radios that carry a GPS:

- **GPS privacy zone**: your location is never sent while you're inside an area you choose (e.g. home).
- **Automatic adverts** on a timer, set the same way as on repeaters.
- **Boot advert**: one zero-hop advert shortly after power-on.

Everything else is EasySkyMesh PowerSaving 18 as released by IoTThinks (based on MeshCore 1.18).
Repeaters, room servers and sensors are unchanged.

> This is not an official EasySkyMesh release. For the official firmware, go to
> [easyskymesh.com](https://easyskymesh.com/) and the [EasySkyMesh releases](https://github.com/IoTThinks/EasySkyMesh/releases).

## 1. Download and Flash

1. Open the latest [Build Companion Firmwares](https://github.com/pmow/EasySkyMesh-GPSZone/actions/workflows/build-companion-firmwares.yml) run.
2. Under **Artifacts**, download the one named after your board, e.g. `RAK_WisMesh_Tag_companion_radio_ble`.
   - nRF52 boards (RAK, WisMesh Tag, T-Echo, …): `.uf2` for drag-and-drop, `.zip` for DFU.
   - ESP32 boards (Heltec, Station, …): `.bin` files, use the `-freshInstall-merged.bin` one for a fresh install.
3. Flash it the same way as EasySkyMesh: see IoTThinks' [instructions to flash custom firmware](https://github.com/IoTThinks/EasySkyMesh/blob/main/firmware/Instruction-to-flash-firmware.md).

You need to be signed in to GitHub to download artifacts. Builds are kept for 90 days.

Every EasySkyMesh companion target is built (`*_companion_radio_usb` and `*_companion_radio_ble`).
These currently fail in EasySkyMesh PowerSaving 18 itself and are not available:
Heltec Mesh Solar (USB and BLE), PicoW BLE, Station G2 BLE.

## 2. GPS Privacy Zone

While the companion's GPS position is inside the zone, it never transmits its location:
adverts go out without lat/lon and GPS telemetry is left out. Messaging and everything else work as normal.

Set it from the MeshCore app's **Command Line** screen (bottom of Settings) or the USB serial console:

| Command | Meaning |
|---|---|
| `zone box <lat1> <lon1> <lat2> <lon2>` | Rectangle from two opposite corners (also turns the zone on) |
| `zone1` … `zone4 <lat> <lon>` | Set one corner of a 4-corner zone, for a slanted shape |
| `zone1` … `zone4 here` | Set a corner to where you are now (needs a GPS fix) |
| `zone` | Show on/off, the corners, and whether you're inside right now |
| `zone on` / `zone off` / `zone clear` | Turn on, turn off, erase |

Example, a box around a house:

```
zone box 52.3720 4.8890 52.3740 4.8920
```

- Coordinates are decimal degrees, **latitude first**. In Google Maps, right-click a spot to copy its coordinates.
- Make the zone a little bigger than you need, because GPS readings can drift by tens of metres.
- Settings are saved and survive reboots.
- Zone commands are **refused over remote CLI (LoRa)**, so the corners can only be read or changed from your own phone or USB.

## 3. Automatic Adverts and Boot Advert

| Command | Range |
|---|---|
| `set advert.interval <minutes>` | 60–240, `0` = off. Zero-hop advert to nearby nodes. |
| `set flood.advert.interval <hours>` | 3–168, `0` = off. Advert flooded across the mesh. |
| `get advert.interval` / `get flood.advert.interval` | Show the current setting. |

Both are off by default. One zero-hop advert is also sent 15 seconds after every boot.

All adverts follow the app's location-sharing setting **and** the GPS privacy zone.

## 4. PowerSaving

PowerSaving works exactly as in EasySkyMesh and is on by default. The main commands are `powersaving on`,
`powersaving off`, `gps on`, `gps off` and `gps sync`; see the
[PowerSaving Wiki](https://github.com/IoTThinks/EasySkyMesh/wiki/PowerSaving) and the
[PowerSaving 18 release notes](https://github.com/IoTThinks/EasySkyMesh/releases/tag/PowerSaving18) for details.

## 5. Building

Use `build.sh`, which turns the zone on (`-DWITH_GPS_PRIVACY_ZONE=1`) for every companion target:

```bash
FIRMWARE_VERSION=gpszone bash build.sh build-firmware RAK_WisMesh_Tag_companion_radio_ble
```

Every push to the `gps-privacy-zone` branch rebuilds all companion firmwares on GitHub Actions
(pushes that only change `.md` files are skipped).

## 6. Credits and Support EasySkyMesh

- EasySkyMesh and PowerSaving are developed by [IoTThinks](https://iotthinks.com/), with contributions from
  the people credited in their [release notes](https://github.com/IoTThinks/EasySkyMesh/releases/tag/PowerSaving18).
- Source this is based on: [IoTThinks/MeshCore, branch PowerSaving-v18](https://github.com/IoTThinks/MeshCore/tree/PowerSaving-v18).
- MeshCore itself: [meshcore-dev/MeshCore](https://github.com/meshcore-dev/MeshCore). Its original README is in [README-MeshCore.md](README-MeshCore.md).
- If you enjoy PowerSaving, support IoTThinks via [PayPal](https://www.paypal.com/ncp/payment/6FGWJJVC572VL) or
  [GitHub Sponsors](https://github.com/sponsors/IoTThinks).

Please report problems with the GPS zone, auto adverts or boot advert **here**, not to IoTThinks.
For PowerSaving itself, use the [EasySkyMesh discussions](https://github.com/IoTThinks/EasySkyMesh/discussions).

License: MIT, the same as MeshCore (see [license.txt](license.txt)).
