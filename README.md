# fysetc_erb_canboot_fix
Instruction and fixes for flashing CanBoot to the fysetc ERB to prevent it from losing firmware on reset


How to flash a fyetc ERB board so it stops losing its flash/fails to enumerate on printer startup/requiring a reset button hit on every boot:

First, flash the ERB using the SKR pico instructions with CanBoot in USB mode:

https://github.com/Polar-Ted/RP2040Canboot_Install/blob/main/README.MD#canboot-for-skr-pico-in-usb-mode

--------------------------------------------

Canboot for SKR Pico in USB mode

    Update to the latest Canboot and Klipper codabase as of 11-8-2022
        Canboot v0.0.1-20-g600967e or newer
        Klipper v0.10.0-623-g5b1a6676 or newer

    SSH to your Klipper host

    CD to Canboot

    cd ~/CanBoot/

Run Make Clean

make clean

Run Make Menuconfig

make menuconfig

Settings:
	MCU Raspberry Pi RP2040
	Build CanBoot deployment 16Kib Bootloader
	COM interface: USB
	(*) support bootloader entry on rapid double click of reset button

> q to exit, Y to save

--------------------------------------------

Run make

> make -j 4

Connect to the ERB by powering it with a separate 24v power supply, and connecting it to the klipper host via USB

While holding the BOOTSEL button (by the USB port), tap the RST button (by the encoder/servo/endstop ports) and release both.  NO JUMPERS

flash the ERB/pico CANBOOT firmware

> sudo make flash FLASH_DEVICE=2e8a:0003

if this does not work, reset the board again, hit "up" on the arrow keys in your terminal client of choice to reload the last command, and try again


--------------------------------------------

Flash klipper on top of the CanBoot firmware

First, get back into your root directory

> cd ..

go into the klipper directory

> cd klipper

run Make Clean

> make clean

configure the klipper firmware for the ERB

> make menuconfig

Settings: 
	MCU Raspberry PI RP2040
	bootloader offset: none
	COM interface: USB

> q to exit, Y to save

> make

Reset the ERB by putting it into bootloader mode by hitting the reset button twice.

> sudo make flash FLASH_DEVICE=2e8a:0003

--------------------------------------------

Your ERB should be ready.
