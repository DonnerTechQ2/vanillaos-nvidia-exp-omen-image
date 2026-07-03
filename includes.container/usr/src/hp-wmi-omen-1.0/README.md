# hp-wmi-omen-fix
A patched out-of-tree version of the `hp-wmi` Linux kernel driver that fixes platform profile registration and fan speed controls/reporting (0 RPM issue) on specific HP OMEN 16 laptops (e.g., motherboard board ID `8BA9`).

## The Issues Fixed

1. **Platform Profile Failing to Register (`-22` / `-EINVAL`):**
   * Certain HP Omen systems have a profile value of `0x44` (representing ECO mode) set in the Embedded Controller (EC) by default at boot.
   * The stock `hp-wmi` driver has no case mapping for `0x44`, causing the initialization of the platform profile handler to fail immediately with `-EINVAL`.
   * **Fix:** Mapped `0x44` to the kernel's `PLATFORM_PROFILE_COOL` option in both get/set profile routines.

2. **0 RPM Fan Speed Reporting:**
   * On motherboard board ID `8BA9`, standard Omen fan query commands (`0x11`) return zero. These models actually route fan speeds and control through the newer Victus-S style query interface (`0x2d` and `0x2e`).
   * **Fix:** Added a fallback helper `is_victus_s_fan()` to automatically direct fan speed reads and writes through the Victus-S WMI commands for motherboard ID `8BA9` without breaking other Omen thermal profiles.

## Installation (DKMS)

DKMS (Dynamic Kernel Module Support) automatically rebuilds the driver when your system updates its Linux kernel.

### Prerequisites

For Ubuntu/Debian:
```bash
sudo apt install dkms linux-headers-$(uname -r)
```

### Setup & Install

1. Clone or download this repository.
2. Copy the source files to `/usr/src/hp-wmi-omen-1.0`:
   ```bash
   sudo cp -r . /usr/src/hp-wmi-omen-1.0
   ```
3. Register the module under DKMS:
   ```bash
   sudo dkms add hp-wmi-omen/1.0
   ```
4. Build and install the module:
   ```bash
   sudo dkms build hp-wmi-omen/1.0
   sudo dkms install hp-wmi-omen/1.0
   ```
5. Reload the module to apply the changes:
   ```bash
   sudo rmmod hp_wmi
   sudo modprobe hp-wmi
   ```

## Usage

### Switch Platform Profiles
You can query and set platform profiles using:
```bash
# View available choices
cat /sys/firmware/acpi/platform_profile_choices

# View active profile
cat /sys/firmware/acpi/platform_profile

# Change profile (e.g. to performance or balanced)
echo performance | sudo tee /sys/firmware/acpi/platform_profile
```

### Manual Fan Controls
You can read fan speeds and override them using standard `hwmon` controls:
```bash
# Check current fan speeds (RPM)
cat /sys/class/hwmon/hwmon5/fan1_input
cat /sys/class/hwmon/hwmon5/fan2_input

# Switch to manual mode
echo 1 | sudo tee /sys/class/hwmon/hwmon5/pwm1_enable

# Set fan speed (0 - 255)
echo 150 | sudo tee /sys/class/hwmon/hwmon5/pwm1

# Restore automatic mode (default)
echo 2 | sudo tee /sys/class/hwmon/hwmon5/pwm1_enable
```
