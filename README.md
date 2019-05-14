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
user@hostname: ~/$ mkdir Jetpack_3.3 && cd Jetpack_3.3
user@hostname: ~/Jetpack_3.3$ mv ~/Downloads/JetPack-L4T-3.3-linux-x64_b39.run .
user@hostname: ~/Jetpack_3.3$ sudo chmod +x JetPack-L4T-3.3-linux-x64_b39.run
user@hostname: ~/Jetpack_3.3$ ./JetPack-L4T-3.3-linux-x64_b39.run
```

3. You will now be greeted by the JetPack installer. Follow the next steps carefully:

  1. Click Next on the first screen:
  <img src="images/tutorial_3.png" width="500"/>
  
  2. Do not change the installation or download directories. Check "No, disable usage collection" and click next:
  <img src="images/tutorial_4.png" width="500"/>
  
  3. Select Jetson TX2 as the development environment:
  <img src="images/tutorial_5.png" width="500"/>
  
  4. Set the Action field to "no action" for Host-Ubuntu. Set the Action field to "mixed" for Target-Jetson TX2/TX2i and expand the package submenu. Set the Action field to "install" for Linux for Tegra Host Side Image Setup. Finally, set the Action field to "no action" for Install on Target. The components manager window should now look like the image below.
  <img src="images/tutorial_6.png" width="500"/>
  
  5. Ater reading the license agreements, select the accept all checkbox and click Accept. When prompted, input your user password.
  <img src="images/tutorial_7.png" width="500"/>
  
  6.Select okay and let all of the installations complete.
  <img src="images/tutorial_8.png" width="500"/>
  
  7. When prompted to flash the operating system to the TX2 select Cancel. We have now installed the required JetPack files onto our remote computer.
  <img src="images/tutorial_9.png" width="500"/>
  
### <a name="packages"></a>
