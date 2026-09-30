# **Installing windows 11 on pre 2012 macs from the start** 

this was written and tested using a 2010 Macbook pro and a 2010 macbook. This guide starts from the beginning, assuming you just installed a new or wiped ssd or HDD. 

# **Inital issues and repairs** 

**Issue:** kernel panic referencing GPU issues and/or no boot when cold (2008 - 2010 macbook pro). **Cause:** C7771 on 2008-2009 Macbook Pros and C9560 on 2010 Macbook Pro often fails causing voltage issues causing the gpu to crash. 

**Solution:** Replace C7771/C9560 with a larger 330uf tantalum capacitor. Alternatively use a program that stops macOS from using the dedicated GPU/Disables it. https://www.youtube.com/watch?v=hu7hPbRrRQ4 

**Problem:** physical damadge, bloated battery, failing HDD, overheating ect. 

**Solution:** Repair and replace components as needed, housing, Battery, SSD, thermal paste. 

Our journy starts with a busted up 2010 15" macbook pro, purchaced from a local flea market for almost nothing. The screen glass was cracked, battery was swolen, HDD was failing, and housing was dented and scuffed. But with power applied it would slowly boot into macOS. I purchaced a used housing, battery and a new 256GB SSD to repair. As a side note, a 2011 macbook pro housing and screen will work for a 2010. 

Skipping ahead to after intalling mac os I was getting a random kernel panic referencing the gpu. The issue 

I found was that C7771 had failed and was causing a kernel panic when macOS tried to utilize the dedicated GPU. I ended up replacing the fauty capacitor with a larger 330uf tantalum capacitor. 

# **Installing macOS** 

**Issue:** Internet recovery for macOS High Sierra fails with "unable to contact recovery server" **Cause:** something broke in the internal SSL security verification or Security.framework causing HTTPS to not work. 

**Solution:** Use terminal to set an NVRAM variable redirecting the installer to connect via HTTP, Alternatively, create a macOS High Sierra bootable Flash drive or DVD and insntall from there. https://mrmacintosh.com/how-to-fix-the-recovery-server-could-not-be-contacted-error-high-sierrarecovery-is-still-online-but-broken\ 

Install macOS by booting to internet recovery by holding  command+option+r keys on boot. intialize and partition the drive using disk utility, I went with APFS since it's supposed to be good for SSDs. click "install mac os" and follow the prompts, if the installer fails with "unable to contact recovery server" you will need to set an NVRAM variable redirecting the installer to connect with HTTP insteas of HTTPS. launch terminal with the menu on the top left and enter "nvram 

IASUCatalogURL="http://swscan.apple.com/content/catalogs/others/index-10.13-10.12-10.11-10.1010.9-mountainlion-lion-snowleopard-leopard.merged-1.sucatalog"" 

you can copy the url from the installer log so that you dont have to type the entire thing in manually. 

# **Installing Windows** 

**Issue:** Attempting to install windows on a GPT Partition scheme under UEFI causes a crash when gpu drivers are loaded. Audio may also be broken. 

**Cause:** Early MacBooks like the  2010 MacBook 7,1 have a rudimentary UEFI that breaks audio and causes NVidia GPU driver crashes on Windows 10/11 if Windows runs in EFI mode. **Solution:** Use Bootcamp to install Windows 10 to a hybrid GPT/MBR partition so it runs under CSM/EFI hybrid mode. Alternatively, install windows to a second drive (replace DVD Drive with a SATA SSD) **Issue:** after installing windows 10/11 under hybrid mode manually using DISM the mac will not boot to the bootcamp partition 

**Solution:** Boot into windows installer and use command prompt to enter "Bootsect /nt60 C: /mbr" where C: is the bootcamp partition windows is installed on. Then enter bootrec /rebuildbcd, this will likely fail, dont worry about it. Then enter bcdboot "C:\Windows /s C: /f BIOS" again, where C: is the bootcamp partition windows is installed on. Alternatively, install windows from a DVD. **Issue:** After installing bootcamp drivers windows windows BSODs shortly after boot. **Cause:** Older versions of macHALdriver.dll cause a BSOD in newer windows versions. **Solution:** Replace macHALdriver.dll with a newer version of bootcamp for a 2017 macbook pro 

## **What you will need:** 

Daemon tools (pick a version that works with your mac os version) 

Windows 7 ISO Windows 10/11 UEFI bootable flash drive A flash drive to store bootcamp drivers 

## **Partitioning with bootcamp** 

**https://www.reddit.com/r/mac/comments/o01d9n/install_latest_windows_10_in_bootcamp_with** Use bootcamp to download the windows support software and drivers to a seperate flash drive NOTE: If installing to a second storage device you can optionally skip the bootcamp partitioning section and go straight to installing windows. You can partition the second drive from within windows if you prefer. Install Daemon tools and mount your windows 7 ISO. 

Bootcamp will now allow you to partition the drive as a hybrid GPT/MBR disk. Pick your partition size and allow your mac to reboot. 

## **Installing windows** 

### **https://gist.github.com/pratyakshm/f19c106205f9327e9f1d538fb91fce65 https://apple.stackexchange.com/questions/470869/bootcamp-not-appearing-as-bootable-disk-on-** 

### **high-sierra** 

Insert your windows bootable flash drive and hold the option key while powering on to get to the boot picker. Select "efi boot" to boot from the windows installer. Once windows is booted press shift+f10 to Use "diskpart"  command to open DiskPart Type "list disk" to list disks on your device. select the disk you want to install to, in my case, for example, type  "select disk 0" to select disk 0. **If installing to a second disk** you can use "create partition primary" to make a new primary partition using the whole disk and then use "format fs=NTFS quick" to quick format the partition with NTFS. you 

can then use "assign letter=C" for example to mount the new partition. 

**If installing to a bootcamp partition** enter"list partition" to list all partitions in the current disk. Select the partition labled "bootcamp", for exaple "select part 4". With the bootcamp partition selected enter "format fs=NTFS quick" to format the bootcamp partition as NTFS. If a drive letter is not assigned use "assign letter=C" for exaple to assign your letter of choice. 

use dism /Get-ImageInfo /ImageFile:X:\sources\install.wim to see the windows aditions available on in our install file. you may need to change the drive letter to the one your flash drive is assosicated with. Note the index number of the edition you want to install. 

use "dism /Apply-Image /ImageFile:X:\sources\install.wim /Index:6 /ApplyDir:C:" where the index number and partition letter match the edition and partition you are trying to install to, to deploy the windows image. After installing enter "Bootsect /nt60 C: /mbr" where C: is the bootcamp partition windows is installed on. Then enter bootrec /rebuildbcd, this will likely fail, dont worry about it. Then enter bcdboot "C:\Windows /s C: /f BIOS" again, where C: is the bootcamp partition windows is installed on. 

## **Booting and settinng up windows** 

**Issue:** After installing bootcamp drivers windows windows BSODs shortly after boot. **Cause:** Older versions of macHALdriver.dll cause a BSOD in newer windows versions. **Solution:** Replace macHALdriver.dll with a newer version of bootcamp for a 2017 macbook pro If all went well you should now be able to boot into your windows install by holding option and selecting it.We arent quite out of the woods yet 

Now for the drivers, 

