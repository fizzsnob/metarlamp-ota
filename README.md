# North METAR Lamp — OTA firmware host

This repo hosts the over-the-air (pull) update files for the North METAR Lamp
firmware. The lamp periodically fetches `firmware.json` over HTTPS, and—if its
`version` is **higher** than the lamp's built-in `FW_VERSION`—downloads and
installs the signed image at `url` (RSA-4096 signature verified on-device
against the key baked into the firmware).

> ## ⚠️ CURRENTLY PUBLISHED: v9079 — SPECIAL ONE-OFF TABBED-UI TRIAL (not production)
>
> The image on this channel right now is a **one-off trial build of the new
> tabbed web setup UI** (built from the `tabbed-setup-ui` branch of the code
> repo). It is versioned **9079** specifically to flag it as a test — it is NOT
> part of the production 78 / 79 / … sequence. Published 2026-06-16 so a lamp can
> try the tabbed interface over the air.
>
> **The production line is v79.** To restore the production channel, revert
> `firmware.img` + `firmware.json` to commit `3385f40` (the v79 release), or
> republish a non-tabbed build from the code repo's `fw-v79` tag / mainline.
>
> **Going forward, every normal OTA push must be the NON-tabbed mainline build —
> not this tabbed trial.**

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
