# PiKVM on ZTE B860H (Amlogic S905X) - Golden Image

[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)

![PiKVM Demo](https://raw.githubusercontent.com/pikvm/pikvm/master/img/logo.png) 

This repository provides a pre-configured "golden image" to turn a ZTE B860H v1/v2 Android TV Box into a fully functional KVM-over-IP device using the powerful [PiKVM](https://pikvm.org/) software.

The image is based on **Armbian Bookworm** and comes with all necessary software (`kvmd`, `ustreamer`) and system configurations (DTB patches for USB OTG) already installed.

## Features

-   **Remote Desktop:** Full KVM (Keyboard, Video, Mouse) access via a web browser.
-   **HDMI Video Capture:** Stream the video output from a target computer. Requires a USB HDMI Capture Card.
-   **HID Emulation:** Send keyboard and mouse inputs to the target computer over a standard USB cable.
-   **Mass Storage Emulation:** Mount ISO/IMG files from an SD card and present them as a virtual USB drive to the target computer. This is perfect for remote OS installations.
-   **Web Terminal:** A full-featured SSH terminal directly in your browser.
-   **ATX Power Control:** (Optional) Control the target computer's power and reset buttons. Requires wiring to GPIO pins.

## Hardware Requirements

1.  **ZTE B860H v1/v2 (Amlogic S905X SoC)**
    *   **Crucial:** The bootloader on the eMMC must be unlocked/replaced to allow booting from an SD card (e.g., using the bootloader from an HG680P).
2.  **USB HDMI Capture Card:** A simple, macro-silicon based dongle is sufficient.
3.  **USB-A to USB-A Cable:** To connect the B860H's OTG port to the target computer.
4.  **SD Card:** A reliable card (16GB or larger recommended) to flash the image.
5.  **Standard Accessories:** Power adapter and an Ethernet cable for network connectivity.

## Quick Start: Using the Golden Image

### 1. Download and Flash the Image

-   Download the latest `PiKVM-For-ZTE-B860H-*.img.zst` file from the [Releases](https://github.com/your-username/your-repo/releases) page of this repository.
-   Decompress the image:
    ```bash
    zstd -d PiKVM-For-ZTE-B860H-*.img.zst
    ```
-   Use a tool like [Balena Etcher](https://www.balena.io/etcher/), [Raspberry Pi Imager](https://www.raspberrypi.com/software/), or `dd` to flash the resulting `.img` file to your SD card.

### 2. Boot and Use from SD Card

-   Insert the flashed SD card into your B860H and power it on.
-   Connect it to your network via LAN.
-   Find its IP address from your router's DHCP client list.
-   Open a web browser and navigate to `https://<your-b860h-ip>`.
-   Log in with the default PiKVM credentials:
    -   **Username:** `admin`
    -   **Password:** `admin`

At this point, your PiKVM is fully functional running from the SD card.

## (Recommended) Install to eMMC

For better performance, reliability, and to free up the SD card slot for the Mass Storage feature, it is highly recommended to install the system to the internal eMMC storage.

1.  While running from the SD card, log in to the B860H via SSH:
    ```bash
    ssh root@<your-b860h-ip>
    ```
    (Default password is `armbian`. You will be prompted to change it on first login).

2.  Run the built-in Armbian installation script:
    ```bash
    sudo armbian-install
    ```

3.  Follow the on-screen prompts, selecting the option to install to eMMC.
4.  Once the process is complete, power off the device:
    ```bash
    sudo poweroff
    ```
5.  **Remove the SD card.**
6.  Power the device back on. It will now boot from the faster eMMC storage, and your PiKVM is ready.

You can now use the SD card slot to host ISO files for the Mass Storage feature.

## For Developers: How to Create a New Golden Image

If you've made improvements and want to create a new distributable image:

1.  Get the system to a perfect state (e.g., on a master SD card or running from eMMC).
2.  Boot the device using a separate "live" Armbian SD card (do not boot from the system you want to image).
3.  Identify the target device to be imaged (e.g., `/dev/mmcblk1` for the eMMC or `/dev/sda` for a USB-mounted SD card).
4.  Create the image using `dd`:
    ```bash
    dd if=/dev/YOUR_TARGET_DEVICE of=PiKVM-For-ZTE-B860H-new.img bs=16M status=progress
    ```
5.  Shrink the image to a minimal size using `pishrink.sh`:
    ```bash
    # (Assuming pishrink.sh is in your current directory)
    sudo ./pishrink.sh PiKVM-For-ZTE-B860H-new.img
    ```
6.  Compress the final image for distribution:
    ```bash
    zstd -19 -T0 PiKVM-For-ZTE-B860H-new.img
    ```

## License

This project, including its configuration and documentation, is licensed under the **GNU General Public License v3.0**. The underlying software (PiKVM, Armbian) is also covered by GPL licenses. See the `LICENSE` file for more details.
