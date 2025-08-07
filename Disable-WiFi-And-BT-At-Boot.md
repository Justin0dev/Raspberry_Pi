

# `Config.txt` Method

Network interfaces can be disabled through the `boot.conf` fiel or by blacklisting kernel modules responsible for network support. A general approach involves modifying the `/boot/config.txt` file or using `raspi-config` to disable onboard WiFi and Bluetooth.


To disable WiFi and Bluetooth add the following lines to the end of the `boot.conf` file to disable WiFi and Bluetooth:  

```
dtoverlay=disable-wifi
dtoverlay=disable-bt
```  

---

# Blacklist Kernel Modules Method  

1. Identify the Kernel Modules
For WiFi and Bluetooth on Raspberry Pi devices, the kernel modules typically involved are brcmfmac for WiFi and btrtl, btbcm, btintel, btqca, and btsdio for Bluetooth. However, these may vary based on the specific Raspberry Pi model and the OS version you're using.

2. Access the Root Filesystem
Insert the SD card into your computer.
You'll need to access the root partition of the SD card, which is formatted with the EXT4 filesystem and might not be directly accessible from Windows computers without additional software. Linux and macOS systems can access this partition more easily.
3. Create or Edit the Blacklist File
Navigate to the /etc/modprobe.d/ directory within the root partition of the SD card.
Create a new file named rpi-blacklist.conf (the name can be anything, as long as it ends with .conf).
Open rpi-blacklist.conf with a text editor (you might need root or administrator permissions to do this).
4. Add Blacklist Entries
Inside the rpi-blacklist.conf file, add lines to blacklist the WiFi and Bluetooth modules. Your entries might look like this:  

```  
blacklist brcmfmac
blacklist brcmutil
blacklist btrtl
blacklist btbcm
blacklist btintel
blacklist btqca
blacklist btsdio
```  
- `brcmfmac` and `brcmutil` are commonly used for WiFi.
- `btrtl`, `btbcm`, `btintel`, `btqca`, and `btsdio` are used for Bluetooth.
5. Save and Exit
After adding the blacklist entries, save your changes and close the text editor.

---  

# Determine Raspberry Pi 5 WiFi/Bluetooth Kernel Modules  

## Check `dmesg` output  

Issue the comand `dmesg` to display kernel boot messages.  Search output for lines mentioning Wifi/Bluetooth drivers being loaded.    
`brcmfmac`  
`sdhci-bcm`  
Might be indicators for Wifi  
`bthhci`  
Might be an indicator for Bluetooth  

## Check `/lib/modules`  

List kernel modules related to Wifi/Bluetooth in your current kernel version. 
`ls /lib/modules/$(uname -r)/kernel/drivers/net/wireless`  
`ls /lib/modules/$(uname -r)/kernel/drivers/bluetooth`  
However, these might not directly translate to the specific modules loaded at boot.

















