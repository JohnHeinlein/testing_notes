# Background information

### Break sequence
`^C` = break into `loader>` prompt.
`^]` and `^r` may also work for some models and under some circumstances. Not really sure.

### ACI vs NXOS

ACI Switches are designed to be deployed as a part of a larger fabric; configured from a management unit.
Image type can be determine by dropping into the `loader>` prompt and checking the image file for an "ACI" prefix.

# Reset Procedures 
### NXOS
- `^C` to drop to `loader>` prompt
- `cmdline recoverymode=1`
- `dir` to check images
- Boot ACI image if NXOS not available
- `switch(boot)#` prompt
  - `write erase`
  - `reload`
- Decline POAP and password prompts, then clean flash from within OS
> [!NOTE]
> Some models will have a separate "kickstart" image that they will attempt to boot prior to the full image. Per page 2 of [this configuration guide](https://www.cisco.com/en/US/docs/storage/san_switches/mds9000/sw/rel_1_x/1_0_2/san-os/configuration/guide/SwImage.pdf), the kickstart image just contains the kernel and basic drivers.
> 
> Some combo of `^C`,`^R`, or `^]` after the kickstart loads will break into the `switch(boot)#` prompt, from which you can run `write erase` as per above.

### ACI Spine/Leaf Reset procedure:
- `^C` to drop to `loader>` prompt
- `cmdline recoverymode=1`
- `dir` to check images
- Boot ACI image if NXOS not available
- login `admin` with no password.
- `(none)#` prompt
- `setup-clean-config.sh`
- `ls /bootflash` to get boot image name
- `setup-bootvars.sh <aci-n9000-dk9.image_name>`
  - This will set the boot variable for firmware for the ACI leaf or spine node.
- cat /mnt/cfg/0/boot/grub/menu.lst.local and cat /mnt/cfg/1/boot/grub/menu.lst.local
  - Verify that each boot partition was set to the `<aci-n9000-dk9.image_name>` that you configured above
- `reload`

Unit will boot into the `(none)#` prompt thereafter, indicating that it is waiting to join a larger fabric.



# Additional info
## Virtual Device Context (VDC)
A VDC is essentially a VLAN on steroids; virtually partitioning the switch into multiple devices akin to virtual machines, i.e. entirely separate command lines and allocated physical resources.
Note: Nexus 5000 series switches do NOT support multiple VDCs. Some VDC information may still be present as the entire switch is considered a monolithic VDC.
VDC documentation for Nexus 7000 Series

## Convert ACI → NXOS
[Guide from Cisco](https://www.cisco.com/c/en/us/td/docs/dcn/nx-os/nexus9000/102x/upgrade/b-cisco-nexus-9000-nx-os-software-upgrade-downgrade-guide-102x/m_converting_from_cisco_nx-os_to_aci_mode_10x.html)
