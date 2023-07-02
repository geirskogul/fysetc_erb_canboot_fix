# fysetc_erb_canboot_fix
Instructions and fixes for flashing CanBoot to the fysetc ERB to prevent it from losing firmware on reset, and fixing the recent bug where the ERB and other RP2040-based board fail to enumerate on SBC restart.

NOTE: This is assuming you have a single board computer (SBC) already running klipper, and you have terminal access (through SSH or other means) to the machine.  This short guide does not cover basic setup of a SBC for klipper, nor any other procedures.
`

First, flash the ERB using the SKR pico instructions with CanBoot in USB mode:

https://github.com/Polar-Ted/RP2040Canboot_Install/blob/main/README.MD#canboot-for-skr-pico-in-usb-mode

I have copied the relevant portions of the instructions here, in case something changes regarding CanBoot later on

--------------------------------------------

Copying the CanBoot files to your SBC:

SSH to your Klipper host

> cd ~/

> git clone https://github.com/Arksine/CanBoot

--------------------------------------------

Configure and flash CanBoot to the ERB:

enter the CanBoot directory

> cd ~/CanBoot/

Run Make Clean

> make clean

Run Make Menuconfig

> make menuconfig

Settings:
	MCU Raspberry Pi RP2040
	Build CanBoot deployment 16Kib Bootloader
	COM interface: USB
	(*) support bootloader entry on rapid double click of reset button

> q to exit, Y to save


Run make

> make -j 4

("-j" specifies the number of threads to run the 'make' command with.  On the CB1 you may need to change it to "make -j 2". Limiting the number of threads eliminates variables between machines for the purposes of these instructions)

Connect to the ERB by powering it with a separate 24v power supply, and connecting it to the klipper host via USB

While holding the BOOTSEL button (by the USB port), tap the RST button (by the encoder/servo/endstop ports) and release both.  NOTE: the ERB does not require any jumpers.  Placing jumpers on it in accordance with the instructions for the EZBRD will blow out the internal 5v power regulator - at that point you can buy a new ERB or deadbug in a dedicated 5v power supply.

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
