# How to Build and Flash Custom Firmware on an Original Prusa i3 MK3S
This guide walks you through the process of setting up the environment, compiling the firmware, and flashing it onto an Original Prusa i3 MK3S printer.

## Prerequisites
- A Debian/Ubuntu-based Linux distribution is recommended.
- Basic familiarity with the terminal.

## Install Required Dependencies
First, update your package list and install the required tools:

```bash
sudo apt-get update
sudo apt-get install -y cmake ninja-build python3-pyelftools python3-polib python3-regex gettext avrdude
```

### Dependencies Explained
- `cmake` and `ninja-build`: Build system tools for configuring and compiling the firmware
- `python3-pyelftools`, `python3-polib`, `python3-regex`, `gettext`: Python utilities for processing translations and building firmware
- `avrdude`: Tool for flashing firmware onto the printer's microcontroller

## Clone the Firmware Repository
Clone the official Prusa Firmware repository:

```bash
git clone https://github.com/prusa3d/Prusa-Firmware.git
cd Prusa-Firmware
```

## Prepare Build Environment (Modifying the Firmware)
If you need to make custom changes (e.g., disabling MINTEMP errors), modify the relevant configuration files:

- For MK3S-specific settings:
`Firmware/variants/MK3S.h`
- Other general settings:
`Firmware/Configuration.h`
`Firmware/Configuration_adv.h`

## Build the Firmware
1. Create a build directory:
```bash
mkdir build
cd build
```
2. Compile the firmware using CMake and Ninja:
```bash
cmake .. -G Ninja -DCMAKE_BUILD_TYPE=Release -DCMAKE_TOOLCHAIN_FILE=../cmake/AvrGcc.cmake -DFW_VARIANTS=MK3S
ninja
```
3. Build Output
After a successful build, the compiled `.hex` file will be located in:
```bash
build/build_gen/MK3S/MK3S_MULTILANG.hex
```
## Cleaning and Rebuilding
If you make further changes to the firmware, clean and rebuild:

```bash
cd ~/Prusa-Firmware/build
ninja clean
ninja
```

## Flash the Firmware
Upload the compiled firmware to your Prusa i3 MK3S:

```bash
avrdude -v -p atmega2560 -c wiring -P /dev/ttyACM0 -b 115200 -D -U flash:w:build/build_gen/MK3S/MK3S_MULTILANG.hex:i
```

- `-v`: Enables verbose output.
- `-p atmega2560`: Specifies the microcontroller type.
- `-c wiring`: Uses the wiring protocol for programming.
- `-P /dev/ttyACM0`: Specifies the serial port.
- `-b 115200`: Sets the baud rate.
- `-D`: Prevents erasing the chip before programming.
- `-U flash`:w:<file>:i: Writes the .hex file to the microcontroller.

## Troubleshooting
- Ensure you have the correct USB cables and drivers installed
- Check that you have the latest version of the firmware repository
- Verify all dependencies are correctly installed

## Additional Resources
- [Einsy RAMBo (MK3/S/+) Board Specifications](https://www.prusa3d.com/product/einsy-rambo-mk3-s/)
- [Prusa Firmware GitHub Repository](https://github.com/prusa3d/Prusa-Firmware)
- [Prusa Firmware Documentation](https://github.com/prusa3d/Prusa-Firmware/blob/MK3/README.md)
