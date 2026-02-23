## Geek Zero

~ is a clone of the Flipper, probably developed somewhere around the Shenzhen area, and the PCB is built from scratch based on the open-sourced schematics available. Usually these devices manifest on AliExpress on seemingly non-salient storefronts. Search for 'Upgraded Clipper', 'Upgraded Clipper with Momentum System', 'Electronic Pet Toy', 'Electronic Dolphin', 'Clipper', 'Geek Zero Dolphin 2' and so on.

 Most of the Geek Zero is electronically identical to the original Flipper Zero, but there are a few differences though.

 So far, the identified ones are:

* The One-Time Programmable memory area (OTP) is NOT programmed, and the 'Security Enclave' along with the 'Factory Keys' are missing.
  * These are NOT open-sourced, so DIY-ers will not be able to use some applications and the universal two-factor (U2F) feature. On the plus side, if the main MCU needs to be replaced, it could be sourced from anywhere.
* The NFC/RFID antenna is smaller, and the 125 kHz part seems not to be tuned as well: it has reduced reading range and it is unable to read FDX-B (animal) tags that are operating at 134 kHz.
* The display is different: not only the pixel aspect ratio is off, but the display controller's preferred contrast value is different too.
* My unit's display backlight is not white: it's green below 25% and very distinctly saffron-esque above 50%, presumably because the poor LEDs are being over-biased.
  * This is fixed now. Poor LEDs had 150 mA blasted at them. Ouch.
* The '5V' pin is labelled as 'VSYS', and is only 5V when the USB is plugged in. Otherwise, it's the lithium battery's voltage. So probably there is no boost converter in it.
* Speaking of the built-in battery, it is very tiny (and optimistically labelled: 760 mAh) and its thermistor wire is not connected at all.
* It has some additional hardware too, allegedly. These are:
  * CC1101 antenna is not only on the PCB with traces, but is also routed out via an MCX connector in the back.
  * [Bosch BMI160](https://www.bosch-sensortec.com/media/boschsensortec/downloads/datasheets/bst-bmi160-ds000.pdf) (the listing calls it 'BM160') inertial sensor.
    * I think it's safe to say that it's missing from mine. The 'BMI Air Mouse' app doesn't work, and there is no Bosch IC on any of the PCBs :)
  * There supposed to be a Hall-sensor on top of the display, but no information about what it is or how it is connected.
    * The white version's backplate shows that it may be read via `PB2` but my black one doesn't say it.
    * I believe it is the missing U23 component. The pinout would match with an [AH1806](https://www.diodes.com/assets/Datasheets/AH1806.pdf), and a small decoupling capacitor is missing too.
    * There is a thin trace that does seem to go to PB2 from the hall sensor's output through a resistor.
  * There is a magnet built in at the back, so you can play with various sensors and switches.

## [Photos](/documentation/geekzero_photos)

There is a scan of the PCBs in the link above.

![](/documentation/geekzero_photos/firmware_reflashed_too_much_contrast_2.jpg)
![](/documentation/geekzero_photos/firmware_reflashed_too_much_contrast_1.jpg)
![](/documentation/geekzero_photos/split_open_2.jpg)
![](/documentation/geekzero_photos/split_open_3.jpg)


## Modified Momentum firmware for the Geek Zero flipper clone

Not much of a modification really. The display is different, and it needed the default contrast value changed.

For the lazy ones, all I did was to change `CONTRAST_ERC` to 10 in `lib/u8g2/u8g2_glue.c`

I also reduced the backlight current. 150 mA for AlGaInP green LEDs were a tad too much. It feels much happier at 20 mA.

`LED_CURRENT_WHITE` was set to  `(20u)` in `targets/f7/furi_hal/furi_hal_light.c`

I tried the qflipper app, and it seems that it needs a valid device name.

`return *furi_hal_version.name == 0x00 ? "geekzero" : furi_hal_version.name;` was added to `targets/f7/furi_hal/furi_hal_version.c` to function `furi_hal_version_get_name_ptr()` around line 269-271, instead of returning `NULL` because the OTP was not set. The name can be changed programmatically too, but it is not implemented in the stock firmware.


## Why

I needed some extra features that were not in the original firmware. Besides, it's an open-source project, and someone went through the trouble of building the device from scratch. Wagner had to exist first, in order to get all the other composers responding to Wagner's work. This is just yet another example of this and [is not the only one](https://www.hackster.io/zst123/fcfz-fully-compatible-flipper-zero-e686ba).

This whole thing started when I re-flashed the Momentum firmware and got a completely black screen. The seller was of course not responsive, so I was on my own. Luckily, the code was relatively easy to navigate and is well-documented.

Sadly, these devices have a certain (bad) reputation because some idiots on social media are (mostly pretending) misusing them; in reality, these devices are in fact nothing but an implementation of a microcontroller ecosystem, just like an Arduino. It just happened to be STM32-based and runs a modified version of RTOS, with some quasi-standardised hardware, and has enough developer community around it so it's above critical mass. As a plus, after the first few years of teething problems, the core developers seemingly stopped randomly introducing breaking changes, so I can actually work with it. Collingridge dilemma and the likes.

For scientific research, I am using my own custom hardware for it that I developed on my own, and for this purpose, I am more than happy to use the stock firmware. Until I hit a snag, that is. Theoretically all other firmware versions may be customised, so far the differences are very little.

## Installing

Clone this repo to your favourite happy place on your computer, format the micro SD card inside the Geek Zero, and then execute:
```
./fbt flash_usb_full
```

The OTP is not programmed. The name of the device is set within the firmware.. U2F will throw a 'Certificate error', but otherwise the device is usable.




<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset=".github/assets/logo_dark.png">
    <source media="(prefers-color-scheme: light)" srcset=".github/assets/logo_light.png">
    <img
        alt="Momentum Firmware"
        src=".github/assets/logo_dark.png">
  </picture>
</p>

<h2 align="center">
  <a href="#Install">Install</a> · <a href="#list-of-changes">Features</a> · <a href="https://discord.gg/momentum">Discord</a> · <a href="#%EF%B8%8F-support">Donate</a>
</h2>

This custom firmware is based on the [Official Firmware](https://github.com/flipperdevices/flipperzero-firmware) for [Flipper Zero](https://flipperzero.one/), and includes most of the awesome features from [Unleashed](https://github.com/DarkFlippers/unleashed-firmware). It is a direct continuation of the Xtreme firmware, built by the same (and only) developers who made that project special.

<br>
<h2 align="center">Modus Operandi</h2>

The goal of this firmware is to constantly push the bounds of what is possible with Flipper Zero, driving the innovation of many new groundbreaking features, while maintaining the easiest and most customizable user experience of any firmware. Fixing bugs promptly and ensuring a stable and compatible system is also of our utmost importance.

- <h4>Feature-rich: We include all third-party features and apps as long as they fulfill a useful purpose and they work correctly, aswell as implement ourselves many new exciting functionalities.</h4>

- <h4>Stable: We ensure the most stable experience possible by having an actual understanding of what's going on, and proactively making all tweaks and additions backwards-, and inter-, compatible.</h4>

- <h4>Customizable: You can tweak just about everything you see: add/remove apps from the menu, change the animations, replace icon graphics, change your Flipper's name, change how the main menu looks, setup different keybinds like never before, and so much more. All on-device, with no complicated configuration.</h4>

<br>

Note that mentioned below are only a few of our staple additions to the firmware. For a full list check [down here](https://github.com/Next-Flip/Momentum-Firmware#List-of-Changes).

<br>
<h2 align="center">Momentum Settings</h2>

We offer a powerful and easy-to-use application tailor-made for our firmware, that lets you configure everything you could dream of, and more:

<img src=".github/assets/settings.png" align="left" height="160vh"/>
<img align="left" height="180vh" width="10" src="https://upload.wikimedia.org/wikipedia/commons/3/3d/1_120_transparent.png">

- <ins><b>Interface:</b></ins> Tweak every part of your Flipper, from the desktop animations, to the main menu, lockscreen behavior, file browser, etc.

- <ins><b>Protocols:</b></ins> Configure SubGhz settings, add/remove custom frequencies, extend SubGhz frequencies to 281-361, 378-481, 749-962 MHz and setup which GPIO pins are used by different external modules.

- <ins><b>Misc:</b></ins> Everything else that doesn't fit the previous categories. Change your Flipper's name, XP level, screen options, and configure the <a href="https://github.com/Z3BRO/Flipper-Zero-RGB-Backlight">RGB backlight</a>.

<br>

<br>

<h2 align="center">Animations / Asset Packs</h2>

We created our own improved Animation / Asset system that lets you create and cycle through your own `Asset Packs` with only a few button presses, allowing you to easily load custom Animations, Icons and Fonts like never before. Think of it as a Theme system that's never been easier.

<img src=".github/assets/packs-folder.png" align="left" width="200px"/>
You can easily create your own pack, or find some community-made ones on <b><a href="https://momentum-fw.dev/asset-packs">our website</a> or on Discord</b>. Check <a href="https://github.com/Next-Flip/Momentum-Firmware/blob/dev/documentation/file_formats/AssetPacks.md">here</a> for a tutorial on creating your own. Essentially, each <code>Asset Pack</code> can configure its own <code>Anims</code>, <code>Icons</code> & <code>Fonts</code>.

<br clear="left"/>

<br>

<img src=".github/assets/packs-select.png" align="left" width="200px"/>
Once you have some asset packs, upload them to your Flipper in <code>SD/asset_packs</code> (if you did this right you should see <code>SD/asset_packs/PackName/Anims</code> and/or <code>SD/asset_packs/PackName/Icons</code>). Alternatively, install directly using the website.


<br clear="left"/>

<br>

<img src=".github/assets/packs-done.png" align="left" width="200px"/>
After installing the packs to Flipper, hit the <code>Arrow Up</code> button on the main menu and go to <code>Momentum Settings > Interface > Graphics</code>. Here choose which asset pack you want and tweak the other settings how you prefer, then exit the app to reboot and enjoy your fully customized Flipper!

<br clear="left"/>

<br>

<h2 align="center">Bad Keyboard</h2>

<img src=".github/assets/badkb.png" align="left" width="250px"/>
BadUSB is a great app, but it lacks a lot of options. Bad-KB allows you to customize all USB and Bluetooth parameters for your attacks.

In Bluetooth mode it allows you to spoof the display name and MAC address of the device to whatever you want. Showing up as a portable speaker or a wireless keyboard is easily doable, allowing you to get the attention of your target without needing a cable at hand.

In USB mode it also enables additional functionality to spoof the manufacturer and product names, as well as vendor and product IDs (VID/PID).

<br>

<h2 align="center">List of changes</h2>

There are too many to name them all, this is a **non-comprehensive** list of the **most notable from an end-user perspective**. For a more detailed list, you can read through the [**changelogs**](https://github.com/Next-Flip/Momentum-Firmware/releases) and commits/code. Also, you can find a **feature comparison with other firmwares** on [our website](https://momentum-fw.dev/).

Note that this repo is always updated with the great work from our friends at [Unleashed](https://github.com/DarkFlippers/unleashed-firmware) and the latest changes from [OFW](https://github.com/flipperdevices/flipperzero-firmware). Below are mentioned only **our** changes that we can actually be credited for, so make sure to check their fantastic additions aswell. And a huge thank you to both teams!

```txt
[Added]

- Momentum App (Easy configuration of features and behavior of the firmware)
- Asset Packs (Unparalleled theming and customization)
- More UI customization, redesigns and optimizations
- Bad-Keyboard App
- BLE Spam App
- FindMy Flipper App
- NFC Maker App
- Wardriver App
- File Search across SD Card
- Additional NFC parsers and protocols
- NFC Type 4 protocol and NTAG4xx support
- Subdriving (saving GPS coordinates for Sub-GHz)
- Easy spoofing (Name, MAC address, Serial number)
- Video Game Module color configuration right from Flipper
- Enhanced RGB Backlight modes (Full customization & Rainbow mode)
- File management on device (Cut, Copy, Paste, Show, New Dir, etc.)
- Remember Infrared GPIO settings and add IR Blaster support in apps
- Advanced Security measures (Lock on Boot, reset on false pins, etc.)
- Disk Image management (Mount and view image contents, open in Mass Storage)
- Extended JavaScript API (Support for UsbDisk/Mass Storage, File operations)
```
```txt
[Updated]

- Enhanced WiFi support for easiest setup ever
- Extended keyboard with cursor movement and symbols
- File Browser with Sorting, More supported File Types
- Advanced and optimized Level System (Up to 30 levels)
- Desktop Keybind system for full key and press/hold remapping
- Storage backend with instant rename and virtual mounting for disk images
- Expanded Sub-GHz App (Duplicate detection & Ignore, Autosave, History improvements)
- Improved Error Messages (Showing source file paths)
```
```txt
[Removed]

- Unused Dummy Mode
- Broken or Superfluous apps
```

<br>

<h2 align="center">Install</h2>

There are 4 methods to install Momentum, we recommend you use the **Web Updater**, but choose whichever one you prefer:

> <details><summary><code>Web Updater (Chrome)</code></summary><ul>
>   <li>Make sure qFlipper is closed</li>
>   <li>Open the <a href="https://momentum-fw.dev/update">Web Updater</a></li>
>   <li>Click <code>Connect</code> and select your Flipper from the list</li>
>   <li>Select which update <code>Channel</code> you prefer from the dropdown</li>
>   <li>Click <code>Install</code> and wait for the update to complete</li>
> </ul></details>

> <details><summary><code>Flipper Lab/App (chrome/mobile)</code></summary><ul>
>   <li>(Desktop) Make sure qFlipper is closed</li>
>   <li>(Mobile) Make sure you have the <a href="https://docs.flipper.net/mobile-app">Flipper Mobile App</a> installed and paired</li>
>   <li>Open the <a href="https://github.com/Next-Flip/Momentum-Firmware/releases/latest">latest release page</a></li>
>   <li>Click the <code>☁️ Flipper Lab/App (chrome/mobile)</code> link</li>
>   <li>(Desktop) Click <code>Connect</code> and select your Flipper from the list</li>
>   <li>(Desktop) Click <code>Install</code> and wait for the update to complete</li>
>   <li>(Mobile) Accept the prompt to open the link in the Flipper Mobile App</li>
>   <li>(Mobile) Confirm to proceed with the install and wait for the update to complete</li>
> </ul></details>

> <details><summary><code>qFlipper Package (.tgz)</code></summary><ul>
>   <li>Download the qFlipper package (.tgz) from the <a href="https://github.com/Next-Flip/Momentum-Firmware/releases/latest">latest release page</a></li>
>   <li>Make sure the <code>WebUpdater</code> and <code>lab.flipper.net</code> are closed</li>
>   <li>Open <a href="https://flipperzero.one/update">qFlipper</a> and connect your Flipper</li>
>   <li>Click <code>Install from file</code></li>
>   <li>Select the .tgz you downloaded and wait for the update to complete</li>
> </ul></details>

> <details><summary><code>Zipped Archive (.zip)</code></summary><ul>
>   <li>Download the zipped archive (.zip) from the <a href="https://github.com/Next-Flip/Momentum-Firmware/releases/latest">latest release page</a></li>
>   <li>Extract the archive. This is now your new Firmware folder</li>
>   <li>Open <a href="https://flipperzero.one/update">qFlipper</a>, head to <code>SD/update</code> and simply move the firmware folder there</li>
>   <li>On the Flipper, hit the <code>Arrow Down</code> button, this will get you to the file menu. In there simply search for your updates folder</li>
>   <li>Inside that folder, select the Firmware you just moved onto it, and run the file thats simply called <code>Update</code></li>
> </ul></details>

<br>

<h2 align="center">Build it yourself</h2>

```bash
To download the repository:
$ git clone --recursive --jobs 8 https://github.com/Next-Flip/Momentum-Firmware.git
$ cd Momentum-Firmware/

To flash directly to the Flipper (Needs to be connected via USB, qFlipper closed)
$ ./fbt flash_usb_full

To compile a TGZ package
$ ./fbt updater_package

To build and launch a single app:
$ ./fbt launch APPSRC=your_appid
```

<h2 align="center">Stargazers over time</h2>

[![Stargazers over time](https://starchart.cc/Next-Flip/Momentum-Firmware.svg?variant=adaptive)](https://starchart.cc/Next-Flip/Momentum-Firmware)

<h2 align="center">❤️ Support</h2>

If you enjoy the firmware please __**spread the word!**__ And if you really love it, maybe consider donating to the team? :D

> **[Ko-fi](https://ko-fi.com/willyjl)**: One-off or Recurring, No signup required

> **[PayPal](https://paypal.me/willyjl1)**: One-off, Signup required

> **BTC**: `1EnCi1HF8Jw6m2dWSUwHLbCRbVBCQSyDKm`

**Thank you <3**
