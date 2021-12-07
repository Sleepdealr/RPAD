# Hardware design for the RP2040/Raspi Pico

## Libraries: 
You might want to import and clone the symbols/fps to your own libs
- Symbol:
	- Created by me
	- `PCB/Libraries/Sleep-lib/`
- Footprint:
	-  Taken from Raspi's implementation
	- `PCB/Libraries/Footprints/RP2040-QFN-56.kicad_mod`
	  
Most of the schematic and PCB layout was taken from the hardware design example provided by Raspberry. The PDF is `Pico-Resources/hardware-design-with-rp2040.pdf`

### Firmware:
- QMK
	-	https://github.com/qmk/qmk_firmware/pull/14877
	-	https://github.com/qmk/qmk_firmware/issues/11649
	-	https://github.com/sekigon-gonnoc/qmk_firmware/tree/rp2040/keyboards/rp2040_example
- Chibios 21.11.x Branch:
	- https://osdn.net/projects/chibios/scm/svn/tree/head/branches/stable_21.11.x/os/hal/ports/RP/
- VIAL
	- [VIAL supposedly working on a rp2040? (Link to VIAL discord)](https://canary.discord.com/channels/798171334756401183/798171646045323265/905160785397968926)
- KMK
	- https://github.com/KMKfw/kmk_firmware
- Keyberon
	- https://github.com/TeXitoi/keyberon

To be able to flash, hold the USB-BOOT button down as you plug in the keyboard (Sorta like Bootmagic reset)

### Flashing QMK on Linux from CLI:

You will need to install picotool with your distro's package manager of choice, or [build it manually](https://github.com/raspberrypi/picotool#building).

In order to be able to flash properly, you will need to configure a custom udev rule.
Open a terminal and execute this script.
```bash
sudo mkdir -p /etc/udev/rules.d/
echo '# /etc/udev/rules.d/99-pico.rules

# Make an RP2040 in BOOTSEL mode writable by all users, so you can `picotool`
# without `sudo`. 
SUBSYSTEM=="usb", ATTRS{idVendor}=="2e8a", ATTRS{idProduct}=="0003", MODE="0666"

# Symlink an RP2040 running MicroPython from /dev/pico.
#
# Then you can `mpr connect $(realpath /dev/pico)`.
SUBSYSTEM=="tty", ATTRS{idVendor}=="2e8a", ATTRS{idProduct}=="0005", SYMLINK+="pico"", TAG+="uaccess", TAG+="udev-acl"' | sudo tee /etc/udev/rules.d/99-pico.rules
sudo udevadm control --reload-rules
sudo udevadm trigger
```