# Surveillance-Robot-using-ROS
# Surveillance Robot using ROS & Raspberry pi (Live feed via networking)


## Introduction

In today’s world , there occurs a lot of natural calamities which includes earthquake, tsunami etc. Making the Army and rescue officers difficult to reach the damaged or destroyed places to rescue people. This project aims in developing a simple surveillance robot giving the live feed of the video from those places to the officials and thus helping in rescue operations.

## Installation of Ubuntu and ROS

* To get into the project of making an surveillance robot using Raspberry Pi and ROS (Robot Operating System ),first we should make sure that ROS is      installed in our Computer.

* ROS is only compatible with Ubuntu operating system and hence Ubuntu OS should be installed in Computer. It will be convenient if we use laptop.

* Try to dual boot the system if we want to maintain two operating systems. Having virtual machine for Ubuntu may cause network errors and so dual     boot is better

* I had used Ubuntu 16.04 

* Installation of ROS : ROS Kinetic is compatible with Ubuntu 16.04 and hence I used ROS   Kinetic
   We can install ROS using the instructions given in the link : http://wiki.ros.org/kinetic/Installation/Ubuntu

* Followed by this link : http://wiki.ros.org/ROS/Tutorials/InstallingandConfiguringROSEnvironment
   ( This link will guide us through the making the catkin package )


## Configuring with Raspberry Pi 3


Now our next step is Interfacing with Raspberry Pi .The need of Raspberry Pi here acts as the mini CPU or mini computer.Instead of giving commands from laptop to laptop. Here we use Raspberry Pi (Master) and our laptop (slave ).We introduce the concept of networking here. Thereby we will give commands from our laptop to operate the Raspberry Pi. We use Raspberry Pi 3 here.

**For configuration of Raspberry Pi 3,**

We need,
* Monitor screen 
* HDMI cable
* Keyboard
* Mouse
* SD card minimum 16 GB or 32 GB

**For configuring Raspberry Pi 3 with Ubuntu mate 16.04 with ROS preinstalled (ISO Image from Ubiquity robots ):**

* Use this link to download the pre-installed Ubuntu 16.04.2 mate with ros , (https://downloads.ubiquityrobotics.com/pi.html)

* Extract the image and write the image to the SD card using "Etcher" : Download link -(https://etcher.io/)

* For Detailed installation refer  (https://www.youtube.com/watch?v=ghImsclr2sU)

* In order to make the pi permanently connected to your  hotspot when it is just powered, use the following tutorial :     (https://www.youtube.com/watch?v=GyP-qY4_K-A)


## Enabling the Network Communication

Follow the steps given below to ensure that SSH is installed in both master and slave.

**Establishing SSH between two ROS systems:**

**Install SSH in Master and Workstation ( slave ):**

*  Check for SSH service in master and workstation :
```
sudo service ssh status
```

*  If SSH service is not found use the following command: 
```
sudo apt-get install openssh-server 
```

* Now that ssh is installed, configure and start ssh using the following commands: 
```
sudo iptables -A INPUT -p tcp –dport ssh -j ACCEPT 
sudo ufw allow ssh 
sudo /etc/init.d/ssh restart 
```

* Find the master’s ip address using the command by using the following command after  wlan0 under inet adddr:. 
```
ifconfig 
```

* On the workstation, open the terminal and start the master. 
```
ssh <name_of_host>@<ip_of_host> 
```

* If there is IO permission error in the host for ROS use the following command to resolve it. 
```
sudo rosdep fix-permissions
```

## Interfacing Arduino with Raspberry Pi 3

We are going to control our robot automation using Arduino and hence the next steps will guide us through the installation of Arduino in the SD card of Raspberry Pi 3.

In this section we are going to give a communication interface between ROS and Arduino :

* Use this link to install Arduino in the Ubuntu OS  https://www.arduino.cc/en/Guide/Linux

    We should use the “ARM linux arduino” zip file ( only that works),since we use Raspberry Pi 3 here. Download the corresponding file from the     Arduino downloads section.

* In order to install the ros serial , the below two commands are used in terminal.
```
sudo apt-get install ros-kinetic-rosserial-arduino
sudo apt-get install ros-kinetic-rosserial
```

* Then we have to install arduino libraries, follow the below command in terminal.
```
cd ~/Arduino/libraries/
rosrun rosserial_arduino make_libraries.py . 
```

* We have to add our user to the OS , so type the below command in terminal and then logout and log in again.
```
sudo adduser $USER dialout
```

* Open arduino -> Sketch -> Include library ->  manage libraries
   Type “rosserial ” in search link and then install the latest version of “Rosserial Arduino Library”

   At the same time follow this link for the catkin package after the installation of Arduino (http://wiki.ros.org/rosserial_arduino/Tutorials/Arduino%20IDE%20Setup)

## Making the Robot automate through commands

* Now we will dump our code into arduino to make it automate when we give commands in the terminal window ( the arduino code I had attached in    the code section or in the repository)

* Run “roscore” in one terminal window

* Now open another terminal and run the below command
```
rosrun rosserial_python serial_node.py _port:=/dev/ttyACM0
```

* Followed by the below command in another terminal window
```
rostopic pub backward std_msgs/Empty –once
or
rostopic pub forward std_msgs/Empty –once
or
rostopic pub left std_msgs/Empty –once
or
rostopic pub right std_msgs/Empty –once
or
rostopic pub stop std_msgs/Empty –once
```

Thus from the above steps we found a way to interface between Raspberry Pi 3 ,arduino and laptop. And succeded in automating the robot by giving commands.


## Interfacing with Raspberry Pi Camera Module

**Raspicam_node**

ROS node for the Raspberry Pi Camera Module. Works with both the V1.x and V2.x versions of the module. We are now going to follow the instructions for interfacing with Camera  module.

**Build Intructions:**

This node is primarily supported on ROS Kinetic, and Ubuntu 16.04, 

* Go to your catkin_ws
```
cd ~/catkin_ws/src
```

*  Download the source for this node by running
```
git clone https://github.com/UbiquityRobotics/raspicam_node.git
```

* Then run, 
```
rosdep update
```

* Install the ros dependencies,
```
cd ~/catkin_ws
rosdep install --from-paths src --ignore-src --rosdistro=kinetic –y
```

* Compile the code with,
```
catkin_make
```

* Connect the Raspberry Pi camera module to the Raspberry Pi 3.

* After the connection is secured, power up Raspberry Pi 3.  Install raspi-config and rpi-update:

```
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install raspi-config rpi-update
```

* Run raspi-config to enable camera:
```
sudo raspi-config
```

* In the configuration tool enable the camera option and then reboot


## LIVE feed from Raspberry Pi Camera

* Install VLC media player in both Raspberry Pi and laptop by using the below command,
```
sudo apt-get install vlc
```

* Followed by the below command in laptop,

```
sudo apt-get install vlc browser-plugin-vlc
```
  
* Try SSH and move into the terminal of Raspberry Pi

* Now run the below command to turn on the Raspberry Pi camera,
```
raspivid -o - -t 0 -hf -w 800 -h 400 -fps 24 |cvlc -vvv stream:///dev/stdin --sout '#standard{access=http,mux=ts,dst=:8160}' :demux=h264
```


* Open VLC player in laptop 

* Go to file -> network stream

* Now add the IP address of the pi as below
```
 http ://IP address of pi:8160
```

* And click on play



## That’s it we can now Live stream the video from Raspberry Pi 3 and can be seen through laptop via networking !!
