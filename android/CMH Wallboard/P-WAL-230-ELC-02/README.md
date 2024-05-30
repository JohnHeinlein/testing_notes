#Reset & patching instructions

## Enter recovery
- Plug USB keyboard in BEFORE BOOT
- Hold RESET button (pinhole on rear) and power on
- Scroll to Factory Reset

## Wipe old apps

## Clear boot splashes

### Bootanimation.zip

### Boot splash baked into kernel
This is the most intesive step, as the static boot splash image is baked into the boot ROM and must be extracted and re-packed.

1) Download imgRePackerRK, Rockchip Driver Assistant, and RKDevTool.
2) Dump boot img
   1) `.\adb shell`
   2) `su`
   3) `ls -l /dev/block/platform/*/by-name/recovery` to get the eMMC boot partition as a block device. For the tested unit, this was `/dev/block/mmcblk1p6`
   4) `exit` shell
   5) `.\adb pull <mmc partition path> <relative destination on host>` to dump partition to host
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
   4) Click `Switch` to swap device into LOADER mode
   5) Once in LOADER, click `Dev Partition` to populate addresse
   6) Check box next to `Boot` entry **ONLY**
      - This ensures that the firmware is only partially flashed
   8) Click `Run` to flash. Device should reboot with the modified splash image.
