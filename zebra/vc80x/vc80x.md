# Factory reset
## Prerequisites
- [Factory reset image](https://www.zebra.com/us/en/support7-downloads/software/operating-system/vc80x-operating-system.html)
- [Android Debug Bridge](https://developer.android.com/tools/adb) command line utility
## Steps
1) Power + Vol Up to enter Android recovery
2) Upgrade via ADB
3) Remove plate/cover from top of unit (4 screws) to access Micro USB port
4) Plug micro USB into PC
5) `adb sideload <img.zip>` with the factory reset image from step 1
# Powering up