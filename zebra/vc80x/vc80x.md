# Factory reset
## Prerequisites
- [Factory reset image](https://www.zebra.com/us/en/support7-downloads/software/operating-system/vc80x-operating-system.html)
- [Android Debug Bridge](https://developer.android.com/tools/adb) command line utility
## Steps
1) Power + Vol Up to enter Android recovery
  - Release when unit vibrates
3) Upgrade via ADB
4) Remove plate/cover from top of unit (4 screws) to access Micro USB port
5) Plug micro USB into PC
6) `adb sideload <img.zip>` with the factory reset image from step 1
7) Reboot. Unit will restart and re-encrypt data format and boot into first-time setup

# Powering up
![power cord pinout](https://github.com/JohnHeinlein/testing_notes/assets/29853148/444d964d-cfa7-4aa0-9549-fbe663e2d70a)

Alternatively, rear panel can be taken off to find the power leads screwed onto the board with convenient terminals.
