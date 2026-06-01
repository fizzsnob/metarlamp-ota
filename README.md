# North METAR Lamp — OTA firmware host

This public repo hosts the over-the-air (pull) update files for the
[North METAR Lamp](https://github.com/) firmware. The lamp periodically fetches
`firmware.json` over HTTPS, and—if its `version` is **higher** than the lamp's
built-in `FW_VERSION`—downloads and installs the signed image at `url`
(RSA-4096 signature verified on-device against the key baked into the firmware).

## Files
- **`firmware.json`** — the manifest the lamp polls. Served at
  `https://raw.githubusercontent.com/fizzsnob/metarlamp-ota/main/firmware.json`.
- **`firmware.img`** — the signed firmware image (uploaded when you cut a release).

Currently `version` is `1` (same as the shipping firmware), so the lamp reports
"up to date" and downloads nothing. Pull-OTA is **armed but not yet live.**

## Publishing an update
1. In the sketch, bump `#define FW_VERSION` (e.g. 1 → 2) and **Export Compiled Binary** in the Arduino IDE.
2. Sign it:  `./tools/ota-sign-release.sh  build/firmware.bin  2  https://raw.githubusercontent.com/fizzsnob/metarlamp-ota/main`
3. Commit the resulting **`firmware.img`** to this repo and bump `version` in `firmware.json` to `2`.
4. The lamp installs it on its next check (or press **Check for Updates** on the setup page).

Security: the image is RSA-signed; the lamp verifies the signature, so a
compromised host cannot push malicious firmware. The HTTPS host cert is pinned
to **ISRG Root X1** (Let's Encrypt) in the firmware.
