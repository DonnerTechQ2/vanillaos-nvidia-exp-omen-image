# Vanilla OS Nvidia + Omen Laptop Patches

A custom Vanilla OS image based on the official Nvidia experimental image, specifically tailored for HP Omen laptops. It integrates custom kernel patches and user-space tools to unlock full manual fan control and dynamic cooling curves directly out of the box.

## Compatibility
- **Base OS:** Built on top of the stable Vanilla OS Orchid (v2).
- **Tested Hardware:** HP Omen 16-wf0xxx
*Note: It may work on other Omen models, but has only been verified on the hardware above.*

## Features & Additions
- **Advanced Thermal Management:** Bundles a graphical utility (Omen Fan Control) to manage dynamic fan curves, calibrate maximum RPMs, and seamlessly switch hardware thermal profiles.
- **HP-WMI Kernel Driver Patch:** Integrates a patched version of the `hp-wmi` DKMS kernel module. This bypasses mainstream kernel restrictions, unlocking hardware-level manual PWM fan control for Omen laptops (removing restrictions that previously limited it to Victus models).
- **Systemd Integration:** Includes a background service to automatically apply safe thermal profiles and fan settings on boot.

## Installation (Using ABRoot)

If you just want to use the pre-built image from this repository:

1. Open a terminal and run the ABRoot configuration editor:
   ```bash
   sudo abroot config-editor
   ```
2. Find the image configuration block and update the `"name"` and `"tag"` fields to point to this repository:
   ```json
   "image": {
       "name": "donnertechq2/nvidia-exp-omen",
       "tag": "dev",
       ...
   }
   ```
3. Save the file and apply the update:
   ```bash
   sudo abroot upgrade -f
   ```
4. Reboot your laptop.

## Building from Source

If you want to fork this project and build it yourself:

### Option 1: GitHub Actions (Recommended)
1. Fork this repository.
2. Go to the **Actions** tab on GitHub and enable workflows.
3. Run the **Vib Build** workflow manually. 
4. Once completed, your image will be published to your GitHub Container Registry (`ghcr.io`).

### Option 2: Local Build (Podman)
You can compile the image locally using the Vanilla Image Builder (`vib`):
```bash
vib compile recipe.yml --runtime podman
```

## Credits & Acknowledgements
- **[arfelious/omen-fan-control](https://github.com/arfelious/omen-fan-control):** For developing the Omen fan control Python utility and maintaining the patched `hp-wmi` DKMS driver.
- **[Vanilla OS Team](https://vanillaos.org/):** For their incredible work in creating such a beautiful and rock-solid operating system.
