# xpad-8bitdo: Impulse Triggers for 8BitDo Ultimate 3-mode on Linux

Modified version of the xpad (DKMS) driver to enable the impulse triggers for the 8BitDo Ultimate 3Mode (2dc8:2030) using the wireless 2.4GHz dongle.

## License

GPL-2.0 (derived from the Linux kernel xpad driver)

## Impulse Trigger modes (trigger_rumble_mode)

- 0 = off: default, triggers are always off
- 1 = mirror: triggers follow strong/weak signals for the controller rumble (default)
- 2 = pressure: game rumble is scaled by how far each trigger is pressed, uses the game rumble to the main motors to make the triggers rumble
- 3 = gc_click: tries to emulate the GameCube trigger click, a short rumble pulse when the
  trigger crosses ~88%/~90% travel, with hysteresis and a softer release tick. Works well
  in Dolphin; pulse strength/duration are tunable in the source (full press sends the full rumble, and the release is at about ~30% strength)

## Install

```Shell
git clone https://github.com/Madoka09/xpad-8bitdo-impulse-triggers.git
cd xpad-8bitdo-impulse-triggers
sudo cp -r . /usr/src/xpad-8bitdo-1.0
sudo dkms install xpad-8bitdo/1.0
sudo modprobe -r xpad && sudo modprobe xpad
```

## To change the trigger mode use

```Shell
echo 2 | sudo tee /sys/module/xpad/parameters/trigger_rumble_mode   # 2 for pressure mode
```

Persist changes across reboots

```Shell
echo "options xpad trigger_rumble_mode=3" | sudo tee /etc/modprobe.d/xpad-trigger-rumble.conf
```

Included script: ./trigger-mode

Can set any configuration mode and make it persistent between reboots

## Tested Hardware

8BitDo Ultimate 3Mode (Jade color) PID: (2dc8:2030) using the wireless dongle

Working on Kernel 7.1.3-cachyos (CachyOS, Plasma 6.7.3)

## What's different?

This is an xpad fork, it maps and gives values to the controller implementation of the impulse triggers so they can properly rumble as they do on compatible software on Windows.

The GIP rumble implementation contains four motor magnitudes in a single packet, two for the strong/weak motors, and two for the L/R triggers. The stock xpad driver has the trigger bytes set as zero, so the impulse triggers never rumble. This fork gives those bytes a value depending on the mode selected, adds trigger-position tracking and full press detection for the pressure and gc_click modes.

## Disclaimer

I built this fork using an LLM to help me understand pieces of code and to improve my experience with this controller, I hadn't written Linux drivers before, so the code was developed via trial and error using my hardware.

Feel free to clone, modify, or use it however you want. Issues and PRs welcome!
