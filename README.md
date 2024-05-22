# ASUS-USB-BT500-Setup-and-Troubleshooting
Setting up and troubleshooting the ASUS USB-BT500 Bluetooth Adapter on Linux Ubuntu, for USC iLab.

## Setting Up the Adapter:

1. Make sure your kernel is up to date. Most updated kernels should already contain the necessary drivers to activate the Bluetooth dongle.

  If you have sudo permissions, enter the following into terminal to make sure your kernel is updated:

```
sudo apt update
sudo apt upgrade
sudo apt install linux-generic
sudo reboot
```
  If you don't have sudo permissions, switch to the root user, and follow the same command as above without the 'sudo' before each line. Be very careful to switch back to your normal user account after running these commands. 

2. Plug in your Bluetooth dongle, and type the following into Terminal to make sure your computer recognizes it:
```
lsusb
```
If there is not a listing that matches your Bluetooth dongle (for ASUS, it should look something like "Bus 001 Device 006: ID 0b05:190e ASUSTek Computer, Inc. ASUS USB-BT500"), try another port, then type the above command again and look for the listing. If none of your ports are able to recognize the device, you may have to try the dongle on another device to make sure it is functioning.

3. Make sure bluez, a common Bluetooth package, is installed, by entering the following into Terminal:
```
bluetoothctl --version
```
If it is not installed, switch to the root user (or type "sudo" before the following command), then type the following into terminal to install it:
```
apt-get install bluez
```

4. While you are still the root user, type the following into Terminal (or, again, if you have sudo permissions, type "sudo" before each of the following lines).
```
systemctl enable bluetooth
systemctl start bluetooth
```

5. Enter the following into Terminal, and make sure the BD Address is something other than **00:00:00:00:00:00**:
```
hciconfig -a
```
If the BD Address is **00:00:00:00:00:00**, try the following steps to troubleshoot.

## Troubleshooting:
1. Check the logs by entering the following into Terminal:
```
dmesg | grep -i bluetooth
```
2. If you are getting an error similar to "firmware file rtl_bt/rtl**8761**bu_fw.bin not found," you will need to navigate to /lib/firmware/rtl_bt and download the files your machine is looking for. To navigate, switch to root user, and enter the following into terminal:
```
cd /lib/firmware/rtl_bt
```
3. Type the following into Terminal, replacing the numbers in the file name at the end of the command with the ones that match **your** file error. For example, my file error that matched my dongle was **8761**, as shown above.
```
wget https://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git/tree/rtl_bt/rtl8761bu_fw.bin
wget https://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git/tree/rtl_bt/rtl8761bu_config.bin
```

4. Now, do the following steps to boot up Bluetooth and check for error messages:
```
cd
lsusb
```
Check to make sure you still see a listing for your Bluetooth dongle. 

5. For the following commands, make sure you're the root user, or type "sudo" before each line if you have sudo permissions.
```
modprobe -r btusb
modprobe btusb
```
This loads the Bluetooth kernel modules.

6. Restart Bluetooth an d check the logs using the following commands:
```
systemctl restart bluetooth
```
```
dmesg | grep -i bluetooth
```
Check for a new error message for any other missing files. If there is a new error, it will show up as a new timestamp before the error. This will also show all previous errors in the log, so make sure there are no **NEW** ones before continuing. If you are still missing files, visit [this github page](https://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git/tree/rtl_bt) to look for those files, and repeat step 2-3, replacing the link in step 3 with whatever Github link contains the missing files from the error message. Then start steps 4-6 over again.

7. Enter the following into Terminal to initialize your adapter. Make sure you're the root user, or type "sudo" before each line if you have sudo permissions.
```
hciconfig hci0 up
```
8. Type the following into Terminal, to make sure the adapter is all set up:
```
hciconfig -a
```
If it is set up and running, the BD Address should come up with something other than 00:00:00:00:00:00. If you open **Settings -> Bluetooth** you should now be able to turn Bluetooth on and connect to nearby devices, although you may have to toggle Bluetooth on and off a few times before it runs properly, or restart Bluetooth one more time following step 6 of the troubleshooting section.


