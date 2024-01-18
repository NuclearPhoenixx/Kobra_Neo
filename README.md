# Kobra Neo Firmware (NuclearPhoenix-Mod)

**This is the most enhanced and modified Marlin-based firmware for the Neo at the moment. See below for an overview of the changes.**

Heavily modified firmware for the Anycubic Kobra Neo. Based on the original by Anycubic, see below for all the changes. This firmware has been changed to be a drop-in replacement of the old firmware. This means that it retains all the original e-steps and PID values and tries to not change some other default values too aggressively. However, unlocks and similar features were kept of course. I also added some new stuff and changed some things up from the original jokubasver fork.

## Features

- Increased default extruder max feedrate from 25 to 50 (now retraction speed is not limited to 25mm/s)
- X and Y default max acceleration increased from 500 to 1000
- Default X and Y jerk increased from 5 to 8
- Enabled extra menu items (Leveling->Edit Mesh + Bed Tramming, Configuration->Controller Fan, Advanced Settings, etc...)
- Enabled "Host Actions" and "Host Action Prompts" with support for the printer UI (allows starting/pausing/resuming of Octoprint prints)
- Increased probing accuracy by doing multiple probes per point
- Increase probing grid from 5x5 to 6x6 points
- Increased speed for the first Z-probe approach when double-probing
- Fixed ABL probe's y-offset coordinates
- Extrapolate bed leveling beyond the 5x5 grid
- Enabled quick home (X and Y homes at the same time)
- Enable M117 Gcode for setting messages to printer screen
- Enable M73 Gcode for setting progress bar on printer screen
- Disabled power-loss recovery by default to help with print quality (can still be enabled manually)
- UI changes:
  - Dark background (not just black!)
  - Removed ugly yellow text color
  - Fixed typos
  - Scroll long file names (also in the SD card menu)
  - Do not blink Z-Offset in the status menu
  - Only blink Print Time in the status menu when idle and not printing
  - Unlocked more languages and enabled missing translations in some menus
  - Fixed the Z-Offset not showing in the Tune menu when printing
  - Automatically open the file menu when connecting an SD Card
- Halfed startup time from about 10s to 5s
- Default values of the original Anycubic firmware for the E-Steps and PID values so you can use this FW as a drop-in replacement for the official FW
- Increased Z-babystepping resolution to 0.01mm from 0.02mm

## A note on Linear Advance (and why it's not enabled)

Unfortunately it seems that uncommenting #define LIN_ADVANCE is not enough to get linear advance to work properly with this version of Marlin and the Kobra Neo. While the feature itself works, there were issues with linear advance and TMC2208 drivers up until around mid 2022, causing the stepper to stall randomly during prints. [This PR](https://github.com/MarlinFirmware/Marlin/pull/24533) seemingly fixes the issue, but merging it to Kobra Neo fork causes weird wobbly stepper movement and prints that look awful. Other options include switching from stealthChop on the extruder to spreadCycle, but that causes the stepper to stall too, there's also SQUARE_WAVE_STEPPING but that reduces the stepper steps in half.

There is a fork of mainline [Marlin for HC32F46x MCUs](https://github.com/shadow578/Marlin-H32) (made to work on an Aquila X2), and I've got a base configuration done for Kobra Neo, but that fork currently does not have support for SoftwareSerial (which is needed for Kobra Neo's TMC2208 drivers that are running in UART mode) and TFT SPI (for our LCD display). Currently looking into it to see if I could port those features over.

## Download

[Kobra_Neo/releases](https://github.com/NuclearPhoenixx/Kobra_Neo/releases)

## Flashing

Copy `firmware.bin` to your microSD card, insert the card with the printer off, turn printer on and wait until you get to the home screen. Afterwards, delete the firmware.bin file from your card.

## (IMPORTANT) Before printing

### Reset EEPROM

After flashing, I recommend to reset your EEPROM, as I found that even after changing some values in the firmware, the printer still used the old values saved in EEPROM.

Ways to reset EEPROM:

- Menu->Configuration->Advanced Configuration->Initiliaze EEPROM;
- Using Prontercace/Octoprint: send M502 and M500;
- Use the EEPROM Editor plugin in Octoprint

### Perform a PID autotune and calibrate your e-steps

The stock firmware does not have great values, so calibrate them as you would normally.

Even after doing all the hardware and mechanical touch-ups, I was not getting great prints. This was solved by calibrating E-steps, as they were quite off (instead of extruding 100mm of filament, my printer extruded 95mm before calibration), so make sure you do that as well.

#### PID Autotune using the menu

- PID Autotune: Go to Menu->Configuration->Advanced Settings->Temperature
- Select PID Autotune E1, and set your printing temperature. Wait for the Autotune to complete.
- Now do the same for PID Autotune Bed.
- Go way back to the Configuration menu and select Store Settings.

#### PID Autotune using Pronterface/Octoprint

If you'd like to perform PID Autotune using Octoprint or Pronterface, follow: [https://teachingtechyt.github.io/calibration.html#pid](https://teachingtechyt.github.io/calibration.html#pid)

#### E-Step Calibration

To perform E-Step calibration, I reccomend to follow this guide: [https://teachingtechyt.github.io/calibration.html#esteps](https://teachingtechyt.github.io/calibration.html#esteps)

#### (Optional) Include your PID and E-step values to firmware

If you are building the firmware yourself, you can include your personal PID and E-step values in the firmware's `Configuration.h` file like this: [https://github.com/jokubasver/Kobra_Neo/commit/a33ebd9](https://github.com/jokubasver/Kobra_Neo/commit/a33ebd9)

Having these values saved in the firmware itself could save a lot of headaches, as If the EEPROM for whatever reason was to clear, your values will not dissapear.

## Building

At the moment, it looks like it's only possible through some ancient software that has been outdated for years. It works, but all the code is really old and messy... Anyways, if you want to compile a `firmware.bin` yourself, go ahead, it's surprisingly easy if you're not a newbie to programming. Here is a great step-by-step tutorial:
[https://www.reddit.com/r/anycubic/comments/y2waxu/tutorial_how_to_build_anycubic_marlin_source_code/](https://www.reddit.com/r/anycubic/comments/y2waxu/tutorial_how_to_build_anycubic_marlin_source_code/)

## Based on

- [jokubasver/Kobra_Neo](https://github.com/jokubasver/Kobra_Neo)
- [sjorge/Kobra_Neo_Fw](https://github.com/sjorge/Kobra_Neo_Fw)
- [jojos38/anycubic-kobra-improved-firmware](https://github.com/jojos38/anycubic-kobra-improved-firmware)
- [Auburn/Kobra_Go](https://github.com/Auburn/Kobra_Go)
- [Kuba1842/Kobra_Neo-Czech](https://github.com/Kuba1842/Kobra_Neo-Czech)

Many thanks to all these people!

## More Info

More info about the printer, its firmware, mainboard and so much more can be found in the [KobraGoNeoInsights](https://1coderookie.github.io/KobraGoNeoInsights/) by [@1coderookie](https://github.com/1coderookie).
