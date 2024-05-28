## Initial factory reset
1. Plug in USB keyboard to port NEAR DC BARREL JACK
2. Power on / Enter Recovery Mode
   - Pinhole button on rear (Recovery/Volume Up) during boot
   - At "No command" screen, hold power button and single press recovery button
3. Select Wipe/Reset & reboot
4. Boots to "Zygote" MDM launcher
5. Enable developer mode & USB debugging from options cog

## Install real launcher
Generally we could just install the APK through ADB, but we want a launcher to remain in-tact after a factory reset.
1. Re-mount system partition as RW
  -  `adb root` and `adb remount` OR
  -  `adb shell mount -o rw,remount /system`
2. Install Nova Launcher as a system app (To survive factory resets)
   1. `adb install <path/to/launcher.apk>`
   2. `adb shell`
   3. `root@rk3288:/ # cp /data/app/<launcher.apk> /system/`
3. Remove custom Zygote MDM launcher
   1. `rm /system/priv-app/zygote_standalone.apk`

## Remove CMH & Zygote apps / configs
Cache: `/data/dalvik-cache/system@priv-app@zygote_standalone.apk@classes.dex`
Data: `rm -r /data/data/com.contextmediainc.system.synop`
Kill any other apps as necessary.

## TWRP & Google Play Services
Since these devices were originally digital signage, they don't include any google play services. These devices also don't have a sideload mode, so we must `dd` the recovery image directly to the appropriate block device.
This will require that recovery.img and the OpenGApps zip both be placed onto an SD card.
1. `adb shell`
2. `su` if not already
3. `DEV=$(ls /dev/block/platform/*/by-name/recovery); ls $DEV` to save recovery block path as a variable
4. `dd of=$DEV if=/mnt/external_sd/recovery.img` to flash the recovery partition
5. `adb reboot recovery` to boot into TWRP
6. Install OpenGApps from the SD card
