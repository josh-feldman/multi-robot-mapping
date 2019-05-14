# Multi Turtlebot3 Exploration in 2D and 3D using NVIDIA Jetson TX2
This writeup includes instructions for integrating an NVIDIA Jetson TX2 and Intel RealSense R200 with a Turtlebot3 Burger. In addition, it includes the requirements for running multiple Turtlebot3 Burgers under a single ROS Master. Finally, it describes how to utilize this repository to perform collaborative multi-robot mapping in 2D and 3D. The Turtlebot3 hardware is all open source, as are the ROS packages used and modified to create this repository. Links to the original packages can be found at the [bottom of this writeup](#packages). This writeup assumes you have access to a computer running Ubuntu 16.04.

## Verify NVIDIA Jetson TX2
1. Plug in and turn on the Jetson while connected to the original development board to start Ubuntu and verify functionality.

2. Remove the Jetson from the development board and secure it to the Orbitty Carrier Board. [This video](https://www.youtube.com/watch?v=9uMvXqhjxaQ) outlines exactly how to switch the boards and includes further details on flashing. For the rest of this setup, we continued to use the wifi antennae provided on the original development board while interfacing through the Orbitty Carrier Board. 

## Prepare JetPack files for flashing
The JetPack installer does not play nicely with the Orbitty Carrier Board. If using the Orbitty Carrier Board for a smaller embedded computer like we did, you must first run the JetPack installer to build all of the required files and then use the Connect Tech. installer to flash the Jetson TX2 with JetPack.

1. On a separate Ubuntu 16.04 computer, head to NVIDIA's [JetPack Archive](https://developer.nvidia.com/embedded/jetpack-archive) and download JetPack 3.3***. You will need an NVIDIA developer account to complete the download, but it is free to sign up using a .edu email address.

2. Make a new directory in the home folder titled "Jetpack_**X.Y**", where X and Y are the version and subversion of Jetpack you are using. We used Jetpack 3.3, so we titled the directory "Jetpack_3.3". Assuming the .run file (ours was called JetPack-L4T-3.3-linux-x64_b39.run) was downloaded to the Downloads folder, run the following commands to make the new directory, move the .run file, allow execution and run the setup,  renaming **"Jetpack_3.3"** with the correct version and subversion if necessary and replacing **"JetPack-L4T-3.3-linux-x64_b39.run"** with the name of your .run file:
```console
user@hostname: ~$ mkdir Jetpack_3.3 && cd Jetpack_3.3
user@hostname: ~/Jetpack_3.3$ mv ~/Downloads/JetPack-L4T-3.3-linux-x64_b39.run .
user@hostname: ~/Jetpack_3.3$ sudo chmod +x JetPack-L4T-3.3-linux-x64_b39.run
user@hostname: ~/Jetpack_3.3$ ./JetPack-L4T-3.3-linux-x64_b39.run
```

3. You will now be greeted by the JetPack installer. Follow the next steps carefully:

    1. Click Next on the first screen:
    <p align="center"><img src="images/tutorial_3.png" width="500"/></p>
  
    2. Do not change the installation or download directories. Check "No, disable usage collection" and click next:
    <p align="center"><img src="images/tutorial_4.png" width="500"/>
  
    3. Select Jetson TX2 as the development environment:
    <p align="center"><img src="images/tutorial_5.png" width="500"/></p>
  
    4. Set the Action field to "no action" for Host-Ubuntu. Set the Action field to "mixed" for Target-Jetson TX2/TX2i and expand the package submenu. Set the Action field to "install" for Linux for Tegra Host Side Image Setup. Finally, set the Action field to "no action" for Install on Target. The components manager window should now look like the image below:
    <p align="center"><img src="images/tutorial_6.png" width="500"/></p>
  
    5. Ater reading the license agreements, select the accept all checkbox and click Accept. When prompted, input your user password:
    <p align="center"><img src="images/tutorial_7.png" width="500"/></p>
  
    6. Select okay and let all of the installations complete:
    <p align="center"><img src="images/tutorial_8.png" width="500"/></p>
  
    7. When prompted to flash the operating system to the TX2 select Cancel. We have now installed the required JetPack files onto our remote computer:
    <p align="center"><img src="images/tutorial_9.png" width="500"/></p>
  
## Flashing the Jetson TX2 on the Orbitty Carrier Board
1. On the Ubuntu 16.04 computer used in the above steps, head to the Connect Tech. [Board Support Package](http://connecttech.com/support/resource-center/nvidia-jetson-tx2-tx1-product-support/) webpage and download the appropriate L4T version for the board and JetPack. As we are using the Jetson TX2 with JetPack 3.3, we downloaded L4T version 28.2x (highlighted in blue below):
<p align="center"><img src="images/tutorial_10.png" width="500"/></p>

2. Assuming the .tgz file was downloaded to the Downloads folder, run the following commands to unzip and run the L4T installer, replacing **"CTI-L4T-V121.tgz"** with the name of the L4T .tgz file installed and replacing the Jetpack_X.Y directory with the correct name if necessary:
```console
user@hostname: ~$ cd Jetpack_3.3/64_TX2/Linux_for_Tegra
user@hostname: ~/Jetpack_3.3/64_TX2/Linux_for_Tegra$ mv ~/Downloads/CTI-L4T-V121.tgz .
user@hostname: ~/Jetpack_3.3/64_TX2/Linux_for_Tegra$ tar -xvf CTI-L4T-V121.tgz
user@hostname: ~/Jetpack_3.3/64_TX2/Linux_for_Tegra$ cd CTI-L4T
user@hostname: ~/Jetpack_3.3/64_TX2/Linux_for_Tegra$ sudo ./install.sh
```
3. After running the installation, put the Jetson into [Forced Recovery Mode](https://www.youtube.com/watch?v=9uMvXqhjxaQ) by pressing and holding POWER, then pressing and holding RESET (POWER should still be held down), then pressing and holding RECOVERY (POWER and RESET should still be held down), and then letting go of RECOVERY, followed by RESET, followed by POWER.

4. Using the Micro-USB to USB-A cable provided with the Jetson TX2, plug the Jetson into the computer used in the above steps. If you run `lsusb` in terminal you should see Nvidia Corp. If so, you are ready to flash the Jetson by running the following command on the computer:
```console
user@hostname: ~/Jetpack_3.3/64_TX2/Linux_for_Tegra$ sudo ./flash.sh orbitty
```

5. Once the script has finished, the Jetson is ready to use. Simply restart, plug in peripherals, and move on to the next steps to set up the kernel and additional drivers.

## Configuring the kernel and installing additional drivers.
1. On the Nvidia Jetson TX2, clone or download JetsonHacks' [buildJetsonTX2 repository](https://github.com/jetsonhacks/buildJetsonTX2Kernel) into the home directory. Enter the directory and run the following in terminal:
```console
nvidia@nvidia-tegra: ~/buildJetsonTx2Kernel-master$ sudo ./getKernelSources.sh
```

2. A kernel configuration window will open. Use the Find Tool (Ctl+F) to check off the following boxes. 3 & 4 below are only required if using a USB Wifi adapter as opposed to the antennae from the original developer board:
    1. CP210x port (For LiDAR)
    2. ACM port (For OpenCR Board)
    3. Networking Support > Wireless > Generic IEEE 802.11 Networking Stack (mac80211) (For ArmadeuS Wifi Adapter)
    4. Device Drivers > Network device support > Wireless LAN > Ralink driver support:
        1. Ralink rt2500 (USB) support
        2. Ralink rt2501/rt73 (USB) support
        3. Ralink rt27xx/rt28xx/rt30xx (USB) support
        
3. Click Save and close and then run the commands:
```console
nvidia@nvidia-tegra: ~/buildJetsonTx2Kernel-master$ sudo ./makeKernel.sh
nvidia@nvidia-tegra: ~/buildJetsonTx2Kernel-master$ sudo ./copyImage.sh
```

4. Restart the Jetson via `sudo reboot -h now`. Once it comes back online, verify that the kernel changes were successful by running the command `ls /dev`. Look for **"ttyACM0"** and **"ttyUSB0"** to ensure teh ACM and CP210 ports are enabled.

5. If using a USB Wifi adapter, plug in the adapter and try to connect to Wifi. If the network connections window shows "Ralink 802.11 n WLAN, Device not ready", run the following commands:
```console
nvidia@nvidia-tegra: ~$ sudo apt-get update
nvidia@nvidia-tegra: ~$ sudo apt-get install linux-firmware
```
## Intel RealSense R200 Drivers and Setup
The [official JetsonHacks instructions](https://www.jetsonhacks.com/2017/03/26/intel-realsense-camera-installation-nvidia-jetson-tx2/) for installing the R200 camera drivers have been modified slightly, but this is a good reference if further assistance is required.

1. On the Nvidia Jetson TX2, download JetsonHacks' [installLibrealsenseTX2 tar.gz](https://github.com/jetsonhacks/installLibrealsenseTX2/releases) into the home directory.

2. Run the following in terminal to unpack the tar.gz file, install the librealsense library, build the UVC driver, modify the kernel, and disable auto-suspend USB port. Replace the name of the .tar.gz file if it has been updated since this posting:
```console
nvidia@nvidia-tegra: ~$ sudo tar -xzf installLibrealsenseTX2-vL4T27.1.tar.gz
nvidia@nvidia-tegra: ~$ cd installLibrealsenseTX2-vL4T27.1
nvidia@nvidia-tegra: ~/installLibrealsenseTX2-vL4T27.1$ sudo ./installLibrealsense.sh
nvidia@nvidia-tegra: ~/installLibrealsenseTX2-vL4T27.1$ sudo ./buildPatchedKernel.sh
nvidia@nvidia-tegra: ~/installLibrealsenseTX2-vL4T27.1$ sudo ./setupTX1.sh
```
### <a name="packages"></a>
