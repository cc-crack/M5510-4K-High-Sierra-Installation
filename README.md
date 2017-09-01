# M5510-4K-High-Sierra-Installation

This repository is based on darkhandz's high sierra repo for XPS15 9550.


The BIOS version for my laptop is 1.3.0 (updated on Aug 28 on Dell's website), thus OsxAptioFix**2**Drv-64.efi and sliding value calculations are needed to boot into the installer and the OS.


You may refer to darkhandz's Chinese tutorial for sliding value calculations. I use `slider=168` for High Sierra and `slider=146` for Sierra, the latter of which being manually calculated.


## The current status:
### Working:
* Built-in speakers, ALCPlugFix needed for a functioning earplug.
* Samsung SM951 works out of the box, no NVME kext needed.
* Intel HD530 with 4K display
* Thunderbolt hotplug (not very stable)
* Brightness slider && tuning
* HWP, with CPU frequency as low as 900MHz.

> For the last two, install the two kexts in POST-INSTALL/LE to /Library/Extensions/ and rebuild kernel cache with the bash script below.
```
sudo rm -rf /System/Library/Caches/com.apple.kext.caches/Startup/kernelcache  
sudo rm -rf /System/Library/PrelinkedKernels/prelinkedkernel  
sudo touch /System/Library/Extensions && sudo kextcache -u /
```

### Not Working:
* FakeSMC sensors, but instead Activity Monitor doesn't crash anymore at Energy tab
* Battery status (charge, percentage) doesn't update on itself. You have to play with it (toggle Show Percentage) to update the value.

Things that are not listed here have not been tested.


## To get HD530 work with 4K:
1. boot the installer with an invalid ig-platform-id in Clover, e.g. `0x12345678`
2. after the system is installed, disable SIP if necessary
3. apply the pixel clock patch below
```
sudo perl -i.bak -pe 's|\xB8\x01\x00\x00\x00\xF6\xC1\x01\x0F\x85|\x33\xC0\x90\x90\x90\x90\x90\x90\x90\xE9|sg' /System/Library/Frameworks/CoreDisplay.framework/Versions/Current/CoreDisplay
sudo codesign -f -s - /System/Library/Frameworks/CoreDisplay.framework/Versions/Current/CoreDisplay
```

4. reboot with a valid ig-platform-id in Clover, e.g. `0x191b0000`

## Issues
* Time Machine works with APFS with no problem (I've migrated pretty much everything from previous backups of 10.12.6 with Migration Assitant)
* The perl script seems to break copy && paste. The problem went away by itself after a few reboots.
* iTunes crashes frequently
  * The crashes do not happen at all with a fake ig-platform-id
