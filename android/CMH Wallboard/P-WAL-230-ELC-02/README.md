#Reset & patching instructions

## Enable debugging
- Plug USB keyboard in BEFORE BOOT
- Hold RESET button (pinhole on rear) and power on
- Scroll to Factory Reset
- Reboot once complete
- Tap cog icon on Zygote MDM
- Enable dev mode through settings

## Replace launcher & wipe app data
- `adb install <path-to-launcher-apk>` to load Nova Launcher
- `adb shell` then `su` for root shell
- `mount -o rw,remount /system` to allow changes to write-protected system parition
- `mv /data/app/com.teslacoilsw.launcher-1 /system/priv-app/` to move Nova Launcher to persistent storage
- Remove Zygote MDM
  - `rm -r /system/priv-app/zygote1`
  - `rm -r /data/data/com.contextediainc.system.zygote` plus any other app data we don't want.
    - Might want to leave the `android.rockchip.update.servce` package and `com.elcld` packages alone, since they're the OEM and I'm not clear what these do.
  - `rm -r /data/dalvik-cache/arm/system@priv-app@zygote1.apk`

## Clear user data
Should be typically safe to just nuke `rm -r /data/media/0/*`, as these are user files will be re-generated.

## Clear boot splashes

### Bootanimation.zip
- `rm /system/media/bootanimation.zip`
Can be replaced with a custom animation, or deleted to fall back to the stock android animation.

### Boot splash baked into kernel
This is the most intesive step, as the static boot splash image is baked into the boot ROM and must be extracted and re-packed.

1) Download imgRePackerRK, Rockchip Driver Assistant, and RKDevTool.
2) Dump boot img
   1) `.\adb shell`
   2) `su`
   3) `ls -l /dev/block/platform/*/by-name/recovery` to get the eMMC boot partition as a block device.
      - e.g. `/dev/block/mmcblk1p6`
      - ![partitions](https://github.com/JohnHeinlein/testing_notes/assets/29853148/5590091c-d806-4a05-913f-e825b94ebf8c)

   5) `exit` shell
   6) `.\adb pull <mmc partition path> <relative destination on host>` to dump partition to host. May need `adb root` first.
      - e.g.  `.\adb pull /dev/block/mmcblk1p6 .\images\boot.img`
3) Unpack & modify boot image
   1) `.\imgRePackerRK.exe boot.img` to unpack
      - Creates a `boot.img.cfg` file and a `boot.img.dump` directory
   2) Replace `boot.img.dump\second.dump\logo.bmp` with a modified file
   3) `.\imgRePackerRK.exe boot.img.cfg` to repack
      - Might be a smaller file size, this is fine.
      - Orginal is renamed boot.img.bak
4) Upload modified image to device
   1) launch RKDevTool.exe
      -  Bottom should read "Found One ADB Device"
   3) Click empty space to far right of "Boot" entry & select repacked img file
   ![load_boot_img](https://github.com/JohnHeinlein/testing_notes/assets/29853148/e9cdb447-d3e0-4cad-b442-37961d0bf739)
   5) Click `Switch` to swap device into LOADER mode
   ![switch_to_loader](https://github.com/JohnHeinlein/testing_notes/assets/29853148/78b501e4-12d5-42ff-9982-e18d74b4e42c)
   7) Once in LOADER, click `Dev Partition` to populate address
   ![dev partition](https://github.com/JohnHeinlein/testing_notes/assets/29853148/daa822bd-a870-4b94-9cba-c9a24b74b837)
   9) Check box next to `Boot` entry **ONLY**
      - This ensures that the firmware is only partially flashed
      ![partial_flash](https://github.com/JohnHeinlein/testing_notes/assets/29853148/cfff1032-48a8-4eef-acb8-9211136767b6)

   10) Click `Run` to flash. Device should reboot with the modified splash image.

