#Multi Robot Exploration in 2D and 3D with a Turtlebot3 & NVIDIA Jetson TX2
This writeup includes instructions for integrating an NVIDIA Jetson TX2 and Intel RealSense R200 with a Turtlebot3 Burger. In addition, it includes the requirements for running multiple Turtlebot3 Burgers under a single ROS Master. Finally, it describes how to utilize this repository to perform collaborative multi-robot mapping in 2D and 3D. The Turtlebot3 hardware is all open source, as are the ROS packages used and modified to create this repository. Links to the original packages can be found at the [bottom of this writeup](#packages).

Configuring the NVIDIA Jetson TX2
----------
1) Plug in and turn on the Jetson while connected to the original development board to start Ubuntu and verify functionality.

2) Remove the Jetson from the development board and secure it to the Orbitty Carrier Board. [This video](https://www.youtube.com/watch?v=9uMvXqhjxaQ) outlines exactly how to switch the boards and includes further details on flashing. For the rest of this setup, we continued to use the wifi antennae provided on the original development board while interfacing through the Orbitty Carrier Board. 

3) Head to NVIDIA's [Jetpack Archive](https://developer.nvidia.com/embedded/jetpack-archive) and download Jetpack 3.3***. You will need an NVIDIA developer account to complete the download, but it is free to sign up using a .edu email address.

4) Place the downloaded .run file (JetPack-L4T-3.3-linux-x64_b39.run) into a directory in the home folder titled "Jetpack_3.3***". You may need to name the directory "Jetpack_X.Y". We used Jetpack 3.3, so we titled the directory "Jetpack_3.3". CD into this directory in terminal and run the following command to allow execution of the program. Replace "JetPack-L4T-3.3-linux-x64_b39.run" with the name of your .run file.
```
nvidia@nvidia-tegra: ~/Jetpack_3.3$ sudo chmod +x JetPack-L4T-3.3-linux-x64_b39.run
nvidia@nvidia-tegra: ~/Jetpack_3.3$ ./JetPack-L4T-3.3-linux-x64_b39.run
```
### <a name="packages"></a>
