# North METAR Lamp — OTA firmware host

This repo hosts the over-the-air (pull) update files for the North METAR Lamp
firmware. The lamp periodically fetches `firmware.json` over HTTPS, and—if its
`version` is **higher** than the lamp's built-in `FW_VERSION`—downloads and
installs the signed image at `url` (RSA-4096 signature verified on-device
against the key baked into the firmware).

Currently `version` is **105** — the build with the new **tabbed web setup UI**,
which is now the mainline. A lamp on an older `FW_VERSION` pulls, verifies, and
installs this signed image; a lamp already on v100 reports "up to date."

## Files
- **`firmware.json`** — the manifest the lamp polls. Served at
  `https://raw.githubusercontent.com/fizzsnob/metarlamp-ota/main/firmware.json`.
- **`firmware.img`** — the signed firmware image (uploaded when you cut a release).

## Publishing an update
1. In the sketch, bump `#define FW_VERSION` and **Export Compiled Binary** (or `arduino-cli compile`).
2. Sign it:  `./tools/ota-sign-release.sh  build/firmware.bin  <version>  https://raw.githubusercontent.com/fizzsnob/metarlamp-ota/main`
3. Commit the resulting **`firmware.img`** here and set `version` in `firmware.json` to `<version>`.
4. The lamp installs it on its next check (or press **Check for Updates** on the setup page).

Security: the image is RSA-signed; the lamp verifies the signature, so a
compromised host cannot push malicious firmware. The HTTPS host cert is pinned
to **ISRG Root X1** (Let's Encrypt) in the firmware.
