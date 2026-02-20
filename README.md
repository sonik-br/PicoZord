# PicoZord (aka RetroZordAdapter)
Pico multiple controller adapter

WORK IN PROGRESS

This is an evolution of my arduino based adadpter. The [RetroZordAdapter](https://github.com/sonik-br/RetroZordAdapter/).

All input and output modes are included on on a single firmware.<br/>
User must configure (via web interface) the desired input and output mode.

## Input modes

- MegaDrive/Genesis
	- Multitap, 3 button, 6 button...
- Saturn
	- Multitap, Saturn Normal pad, Fighting stick, Twin Stick, Saturn Analog pad, Racing wheel...
- Famicom/Nes
	- Nes pad...
- Super Famicom/Snes
	- Multitap, Snes pad, Snes NTT pad...
- PlayStation 1/2
	- Multitap, Digital pad, Analog Stick, DualShock, DualShock 2, Guncon, NeGcon, Volume Paddle, JogCon, Densha de GO, Jet de GO...
- Wii
	- Classic Controller, Classic Pro Controller, Nes Classic Controller, Snes Classic Controller, Nunchuk...
- VirtualBoy
	- Gamepad...
- Nintendo 64
	- Gamepad...
- GameCube
	- Wired controller, Wireless controller (WaveBird), ~~Racing wheel~~...
- PCEngine
	- Multitap, 2 button controller, 3 button controller, 6 button controller...
- NeoGeo
	- Gamepad...
- 3DO
    - Gamepad...
- Jaguar
    - Gamepad...
- Custom
	- ~~Customizable DIY controller with digital and analog features. Easy to setup.~~
- USB
	- Most Xbox/360/One/Series controllers
	- Most PS3/PS4/PS5 controllers
	- Most Switch controllers
 	- Some USB Hubs for multiple controller input
	- Check [here](https://github.com/sonik-br/pico_input_usb_tester?tab=readme-ov-file#rough-list-of-supported-devices) for a more complete list

## Output modes

| Mode          | # Devices | Rumble | Analog Trigger/Button/Dpad | Notes                       |
|---------------|-----------|--------|----------------------------|-----------------------------|
| HID           | Up to 6   |        | Trigger                    | Generic DINPUT              |
| HID_MISTER    | Up to 6   |        |                            | MiSTer specific mode        |
| XINPUT *      | 1         | Yes    | Trigger                    | Generic Xinput for PC/X360  |
| XINPUTW       | 4         | Yes    | Trigger                    | Xinput for PC only          |
| XID           | 1         | Yes    | Trigger, Button            | ogXbox                      |
| PS3           | 1         | Yes    | Trigger, Button, Dpad      | PS3/RPCS3                   |
| PS4 **        | 1         | Yes    | Trigger                    | PS4                         |
| SWITCH        | Up to 6   |        |                            | Generic Switch              |
| SWITCH_PRO    | Up to 6   | Yes    |                            | Switch Pro / NSO controller |
| GCWIIU        | Up to 4   | Yes    | Trigger                    | WUP-028 for Dolphin/WiiU/NS |
| PANTHERLORD   | Up to 2   | Yes    |                            | Generic PS2 to USB adapter  |
| MDMINI        | 1         |        |                            | MegaDrive Mini              |
| PSCLASSIC     | 1         |        |                            | PlayStation Mini            |
| PCEMINI       | 1         |        |                            | PCEngine Mini               |
| NEOGEOMINI    | 1         |        |                            | NeoGeo Mini                 |
| ASTROCITYMINI | 1         |        |                            | AstroCity Mini              |
| EGRETMINI     | 1         |        |                            | Egret Mini                  |


\* XINPUT mode does work on console if using UsbdSecPatch.<br/>
\** PS4 authorization requires using extracted keys from a controller, or a controller/dongle/adapter on the USB HOST port.<br/>
It might require using a usb hub too. Please note that some usb hubs are not compatible.<br/>
DualShock 4 v1 (CUH-ZCT1x) is not compatible for console auth.

`HID_MISTER` can automatically change to a Special Mode when a special controller is detected during pico's initialization.

### Special output modes

| Mode        | # Devices | Notes                 |
|-------------|-----------|-----------------------|
| NEGCON      | 1?        | MiSTer Wheel/Paddle   |
| JOGCON      | 1?        | MiSTer Spinner/Paddle |
| JOGCONMOUSE | 1?        | Gamepad + Mouse       |
| GUNCON      | 1?        | MiSTer Lightgun       |

## Configuration

Put the device into webconfig mode, then open [sonik-br.github.io/PicoZord](https://sonik-br.github.io/PicoZord/).

To put the adapter in webconfig:<br/>
During initialization, the led will blink for some seconds. Just press BOOTSEL or START during that time window.

## Input Wiring

~~Multiple input ports (of same type) can be easily added with small code change.~~

Suggested pins for single input type. Can be changed on source code.
																										  
| RP2040  | USB  | WII  | PSX       | N64        | GAMECUBE   | MD    | SAT   | VBOY   | NES    | SNES    | PCE   | NEOGEO    | 3DO   | Jaguar |
|---------|------|------|-----------|------------|------------|-------|-------|--------|--------|---------|-------|-----------|-------|--------|
| 0       | D+   |      |           |            |            |       |       |        |        |         |       |           |       |        |
| 1       | D-   |      |           |            |            |       |       |        |        |         |       |           |       |        |
| 2       |      |      | 9 ACK *1k |            |            |       |       |        |        |         |       |           |       |        |
| 3       |      |      | 2 CMD     |            |            |       |       |        |        |         |       |           |       |        |
| 4       |      |      | 1 DAT *1k | 2 DATA *1k | 2 DATA *1k |       |       |        |        |         |       |           |       |        |
| 5       |      |      | 6 ATT     |            |            |       |       |        |        |         |       |           |       |        |
| 6       |      |      | 7 CLK     |            |            |       |       |        |        |         |       | 03 SELECT |       | 04 J0  |
| 7       |      |      |           |            |            | 1 D0  | 3 D0  | 4 CLK  | 2 CLK  | 2 CLK   | 2 D0  | 15 UP     |       | 14 J8  |
| 8       |      |      |           |            |            | 2 D1  | 2 D1  | 2 LAT  | 3 LAT  | 3 LAT   | 4 D2  | 07 DOWN   |       | 13 J9  |
| 9       |      |      |           |            |            | 3 D2  | 8 D2  | 1 DAT  | 4 DAT  | 4 DAT1  | 5 D3  | 14 LEFT   |       | 12 J10 |
| 10      |      |      |           |            |            | 4 D3  | 7 D3  |        |        | 5 DAT2* | 3 D1  | 06 RIGHT  |       | 11 J11 |
| 11      |      |      |           |            |            | 6 TL  | 6 TL  |        |        | 6 SEL*  | 6 SEL | 13 A      | 6 OUT | 06 B0  |
| 12      |      |      |           |            |            | 9 TR  | 5 TR  |        |        |         | 7 CLR | 05 B      | 9 IN  | 10 B1  |
| 13      |      |      |           |            |            | 7 TH  | 4 TH  |        |        |         |       | 12 C      | 7 CLK | 03 J1  |
| 14      |      | SDA  |           |            |            |       |       |        |        |         |       | 04 D      |       | 02 J2  |
| 15      |      | SCL  |           |            |            |       |       |        |        |         |       | 11 START  |       | 01 J3  |
| GND     | GND  | GND  | 4 GND     | 3 GND      | 3, 4 GND   | 8 GND | 9 GND | 5 GND  | 1 GND  | 7 GND   | 8 GND | 01 GND    | 8 GND | 09 GND |
| VBUS/5V | VBUS |      | 3 5V      |            | 1 5V       |       |       |        |        |         |       |           |       |        |
| 3V3     |      | 3.3V | 5 3.3V    | 1 3.3V     | 6 3.3V     | 5 5V  | 1 5V  | 2 5V   | 5 5V   | 1 5V    | 1 5V  | 08 5V     | 5 5V  | 07 5V  |

*1k = 1k resistor pullup to 3.3V

DAT2 and SEL are optional on SNES connector. Only used for multitap support.

PSX/N64/GC requires pullup on some pins.

PSX controllers uses ~7.5V for rumble. 5V works but it's weaker.
Add an external PSU or a power booster if 7.5V is needed.

N64/GC uses PIO resources. USB HOST too. It's not possible to use multiple N64/GC input ports and a USB HOST port at the same time.<br/>

#### A note about 3.3V vs 5V

RP2040 runs with 3.3V and is not 5V tolerant!

Some controllers run with 5V power and 5V data lines (ie: MD/SAT/VBOY/NES/SNES/PCE/NEOGEO).

The wiring above is using 3.3V power/data for all systems.

All controllers tested for the systems in the table worked fine with 3.3V. (expect 3do and jaguar. Those are UNTESTED)<br/>
If you really want to use 5V on for specific systems, be sure to level-shift (3.3V <-> 5V) all data pins and to power te controller with 5V.

This does not apply to the optional USB HOST port. It can be directly wired to the pico and powered with 5V.

#### Warning
Code and wiring directions are provided to you 'as is' and without any warranties. Use at your own risk.
