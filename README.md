# apex-steamos-fingerprint-fix

A utility for automatically applying the fix for the **OneXPlayer Apex** handheld console running **SteamOS**. This script specifically addresses the issue where the fingerprint sensor wakes the device from sleep.

## DISCLAIMER

**Important:** The fix provided by this script is **not part of the official SteamOS** and is offered **as-is**. Valve and the SteamOS development team are **not responsible** for any issues that may arise from using this script. This fix was created by the community to address specific hardware quirks on SteamOS and is provided for convenience only.

Use it at your own risk.

## 📌 What this script does

The script applies the following stages, each reported with its own status (`done`, `not needed`, or `error`):

- **Preparation** — Validates that the script is running on SteamOS and checks that the hardware is indeed a OneXPlayer Apex. If any check fails, the script exits without modifying anything.

- **Fingerprint sensor tweaks** — A light touch on the power button's fingerprint sensor would wake the device from sleep, which is annoying when carrying it in a bag. The script disables this via two independent paths:
  - **PCIe PME wake** of the reader's xHCI controller (applied instantly via runtime sysfs and made permanent with a persistent udev rule in `/etc/udev/rules.d/`).
  - **GPIO wake line** (applied by appending the `gpiolib_acpi.ignore_wake=AMDI0030:00@58` kernel argument to `/etc/default/grub` and rebuilding the bootloader configuration using `update-grub`).

The script is **idempotent** – running it multiple times won't make unnecessary changes. If the fix is already applied, it will simply report `not needed`.

## ⚙️ Requirements

- Hardware: **OneXPlayer Apex**
- OS: **SteamOS** (any recent version)
- **root** privileges (the script will request sudo automatically)
- **A password set for the user `deck`** (required for `sudo` to work)
- Internet connection (for downloading the script)

## 🚀 Installation and Usage

### Prerequisite: Set a password for the `deck` user

By default, SteamOS does not have a password set for the `deck` user, which means `sudo` will fail when the script tries to request root privileges.

Before running the fix, open a terminal in Desktop Mode and set your password:
```bash
passwd
```
You will be prompted to enter your new password twice.

### Run the script

Once your password is set, copy and run this **single command** in your terminal:

```bash
curl -fsSL https://raw.githubusercontent.com/Welld1s/apex-steamos-fingerprint-fix/main/install.sh | sh
```

The script will prompt for your `deck` user password. Each stage prints its own status line, and the final summary tells you whether a reboot is required.

## ⚠️ Important Notes

- **A reboot is required** after the kernel argument is added. The PCIe PME fix works immediately, but the GPIO kernel argument will only take effect after you restart the system.

- **BIOS setting** — when the fingerprint sensor tweaks stage adds the GPIO kernel argument, it is strongly recommended to enter the BIOS (usually by pressing `Volume Up` + `Power` or `Del` during boot) and set: `Advanced -> ACPI Settings -> Enable ACPI Auto Configuration` -> **Enabled**. This is necessary for proper suspend/resume behavior.

- The script is safe to run multiple times – it won't duplicate kernel arguments or overwrite already correct settings.

- If you are not on SteamOS or not on a OneXPlayer Apex, the script exits during the **Preparation** stage with a clear error message and does not modify anything.

## 🙏 Credits

This script wouldn't exist without the work of the following people:

- **[srsholmes](https://github.com/srsholmes)** – for the original fingerprint sensor fix research. His work forms the foundation of this script.

Thank you for your contribution to the OneXPlayer community!

## 📄 License

MIT License – use, modify, and distribute freely.

## 🤝 Contributing

If you find a bug, want to improve the SteamOS compatibility, or want to add support for other OneXPlayer models, feel free to open an Issue or submit a Pull Request.

**Made for OneXPlayer Apex owners on SteamOS.**
