# PicoZord
Pico multiple controller adapter

WORK IN PROGRESS

This is an evolution of my arduino based adadpter. The [RetroZordAdapter](https://github.com/sonik-br/RetroZordAdapter/).

## Input modes

- MegaDrive/Genesis
	- Multitap, 3 button, 6 button...
- Saturn
	- Multitap, Saturn Normal pad, Fighting stick, Twin Stick, Saturn Analog pad, Racing wheel...
- Famicom/Nes
	- Nes pad...
- Super Famicom/Snes]
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
- ~~3DO~~
- ~~Jaguar~~
- Custom
	- Customizable DIY controller with digital and analog features. Easy to setup.
- USB
	- Most Xbox/360/One/Series controllers
	- Most PS3/PS4/PS5 controllers
	- Most Switch controllers
	- ~~Check here for a more complete list~~

## Output modes

| Mode        | # devices | Rumble | Analog Buttons | Analog Dpad |
|-------------|-----------|--------|----------------|-------------|
| HID_GENERIC | up to 6   |        |                |             |
| XINPUT *    | 1         | Yes    |                |             |
| XID         | 1         | Yes    | Yes            |             |
| PS3         | 1         | Yes    | Yes            | Yes         |
| PS4 **      | 1         | Yes    |                |             |
| SWITCH      | up to 6   |        |                |             |
| SWITCH_PRO  | up to 6   | Yes    |                |             |
| GCWIIU      | up to 4   | Yes    |                |             |
| ~~PANTHERLORD~~ | ~~up to 2?~~  | ~~Yes~~    |                |             |

\* XINPUT mode does not work on console.<br/>
\** PS4 authorization requires using a controller, dongle or adapter on the USB HOST port.<br/>
It might require using a usb hub too. Please note that some usb hubs are not compatible.<br/>
DualShock 4 v1 (CUH-ZCT1x) is not compatible.

`HID_GENERIC` can automatically change to a Special Mode when a special controller is detected during pico's initialization.

### Special output modes

| Mode        | # devices | Obs.                  |
|-------------|-----------|-----------------------|
| NEGCON      | 1?        | MiSTer Wheel/Paddle   |
| JOGCON      | 1?        | MiSTer Spinner/Paddle |
| JOGCONMOUSE | 1?        | Gamepad + Mouse       |
| GUNCON      | 1?        | MiSTer Lightgun       |

## Input Wiring

Multiple input ports (of same type) can be easily added with small code change.

~~Multi inpute mode with selection might happen in future update~~

Suggested pins for single input type. Can be changed on source code.
																										  
| RP2040  | USB  | WII  | PSX       | N64        | GAMECUBE   | MD    | SAT   | VBOY   | NES    | SNES    | PCE   | NEOGEO    |
|---------|------|------|-----------|------------|------------|-------|-------|--------|--------|---------|-------|-----------|
| 0       | D+   |      |           |            |            |       |       |        |        |         |       |           |
| 1       | D-   |      |           |            |            |       |       |        |        |         |       |           |
| 2       |      |      | 9 ACK *1k |            |            |       |       |        |        |         |       |           |
| 3       |      |      | 2 CMD     |            |            |       |       |        |        |         |       |           |
| 4       |      |      | 1 DAT *1k | 2 DATA *1k | 2 DATA *1k |       |       |        |        |         |       |           |
| 5       |      |      | 6 ATT     |            |            |       |       |        |        |         |       |           |
| 6       |      |      | 7 CLK     |            |            |       |       |        |        |         |       | 03 SELECT |
| 7       |      |      |           |            |            | 1 D0  | 3 D0  | 4 CLK  | 2 CLK  | 2 CLK   | 2 D0  | 15 UP     |
| 8       |      |      |           |            |            | 2 D1  | 2 D1  | 2 LAT  | 3 LAT  | 3 LAT   | 4 D2  | 07 DOWN   |
| 9       |      |      |           |            |            | 3 D2  | 8 D2  | 1 DAT  | 4 DAT  | 4 DAT1  | 5 D3  | 14 LEFT   |
| 10      |      |      |           |            |            | 4 D3  | 7 D3  |        |        | 5 DAT2* | 3 D1  | 06 RIGHT  |
| 11      |      |      |           |            |            | 6 TL  | 6 TL  |        |        | 6 SEL*  | 6 SEL | 13 A      |
| 12      |      |      |           |            |            | 9 TR  | 5 TR  |        |        |         | 7 CLR | 05 B      |
| 13      |      |      |           |            |            | 7 TH  | 4 TH  |        |        |         |       | 12 C      |
| 14      |      | SDA  |           |            |            |       |       |        |        |         |       | 04 D      |
| 15      |      | SCL  |           |            |            |       |       |        |        |         |       | 11 START  |
| GND     | GND  | GND  | 4 GND     | 3 GND      | 3, 4 GND   | 8 GND | 9 GND | 5 GND  | 1 GND  | 7 GND   | 8 GND | 01 GND    |
| VBUS/5V | VBUS |      | 3 5V      |            | 1 5V       |       |       |        |        |         |       |           |
| 3V3     |      | 3.3V | 5 3.3V    | 1 3.3V     | 6 3.3V     | 5 5V  | 1 5V  | 2 5V   | 5 5V   | 1 5V    | 1 5V  | 08 5V     |

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

All controllers tested for the systems in the table worked fine with 3.3V.<br/>
If you really want to use 5V on for specific systems, be sure to level-shift (3.3V <-> 5V) all data pins and to power te controller with 5V.

This does not apply to the optional USB HOST port. It can be directly wired to the pico and powered with 5V.

#### Warning
Code and wiring directions are provided to you 'as is' and without any warranties. Use at your own risk.