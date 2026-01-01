# A guide to flashing the Dell PERC H310 RAID controller to HBA mode (aka IT Mode)

![H310 photo](https://github.com/ThinkPadThink/GuideFLASH-LSI/blob/master/h310.jpg?raw=true)  
*This guide is a translation of another guide, [original guide](https://tylermade.net/2017/06/27/how-to-crossflash-perc-h310-to-it-mode-lsi-9211-8i-firmware-hba-for-freenas-unraid/)*

*__When copying this information, always cite the original source.__*  

---

## Why would you need this?

For example, if you have an unused controller and want to use it for simple disk connections or for software RAID (ZFS, LVM, Storage Spaces, etc.) or just for connecting SAS/SATA drives.  

---

## What do you need?

* A SAS controller  
* A USB flash drive, any size will work, e.g., 1 GB  

**WARNING: WHAT YOU DO TO YOUR CONTROLLER IS AT YOUR OWN RISK!!**  

---

# Preparation and flashing

**Note:** If your motherboard uses UEFI, this guide may not work! As an alternative, use this guide: [techmattr.wordpress.com](https://techmattr.wordpress.com/2016/04/11/updated-sas-hba-crossflashing-or-flashing-to-it-mode-dell-perc-h200-and-h310/)  

1. Download [Rufus](https://rufus.ie), [FreeDOS Boot Floppy](http://www.freedos.org/download/download/FD12FLOPPY.zip), and the [LSI9211-8i archive](https://www.mediafire.com/file/6mtie10d9ud6675/LSI-9211-8i.zip/file);  
2. Format your USB stick using Rufus to FreeDOS as shown below;  
![Rufus photo](https://github.com/ThinkPadThink/GuideFLASH-LSI/blob/master/rufus.jpg?raw=true)  
3. Extract the FreeDOS Boot Floppy archive to the USB drive and overwrite existing files, then delete `SETUP.BAT`;  
4. Extract the LSI9211-8i archive to the USB drive and overwrite existing files;  
5. Shut down your PC, install the controller, and boot into FreeDOS;  
6. Enter the command:  
```
megacli.exe -AdpAllInfo -aAll -page 20
```  
Press *Enter* and scroll to the *HW Configuration* section. Write down or photograph the *SAS Address* as shown below. **If you skip this, your controller may become bricked in the next steps**;  
![SAS Address photo](https://github.com/ThinkPadThink/GuideFLASH-LSI/blob/master/SAS%20Address.jpg?raw=true)  
7. Enter:  
```
megarec.exe -writesbr 0 sbrempty.bin
```  
before flashing;  
![Empty photo](https://github.com/ThinkPadThink/GuideFLASH-LSI/blob/master/empty.jpg?raw=true)  
8. Enter:  
```
megarec.exe -cleanflash 0
```  
to clear the firmware, then reboot into FreeDOS using the `reboot` command;  
9. After reboot, enter:  
```
sas2flsh.exe -o -f 6GBPSAS.fw
```  
10. Enter:  
```
s2fp19.exe -o -sasadd %YOUR SAS Address%
```  
Replace `%YOUR SAS Address%` with the SAS address you saved in step 6. For example:  
```
s2fp19.exe -o -sasadd 5c81f660dbbb1a00
```  
![SAS Address photo](https://github.com/ThinkPadThink/GuideFLASH-LSI/blob/master/sasadd.jpg?raw=true)  
11. Reboot into FreeDOS again using the `reboot` command;  
12. After reboot, enter:  
```
sas2flsh.exe -o -f 2118it.bin
```  
When prompted with *Would you like to flash anyways?*, type `y` and press *Enter*;  
![Flashing photo](https://github.com/ThinkPadThink/GuideFLASH-LSI/blob/master/2118it.jpg?raw=true)  
![Confirm flash photo](https://github.com/ThinkPadThink/GuideFLASH-LSI/blob/master/yes.jpg?raw=true)  
13. Done! Reboot into Windows/Linux. It should now appear as Dell HBA 6 Gbit/s in Windows and similarly in Linux.  

---

**PS:** This guide should theoretically also work for the IBM M1015 controller and the original LSI MegaRAID 9240.  

![M1015](https://github.com/ThinkPadThink/GuideFLASH-LSI/blob/master/ibm%20m1015.jpg?raw=true)  
![9240](https://github.com/ThinkPadThink/GuideFLASH-LSI/blob/master/9240.jpg?raw=true)  

---

## Links

* [Original guide](https://tylermade.net/2017/06/27/how-to-crossflash-perc-h310-to-it-mode-lsi-9211-8i-firmware-hba-for-freenas-unraid/)  
* [Alternative guide for UEFI motherboards](https://techmattr.wordpress.com/2016/04/11/updated-sas-hba-crossflashing-or-flashing-to-it-mode-dell-perc-h200-and-h310/)
