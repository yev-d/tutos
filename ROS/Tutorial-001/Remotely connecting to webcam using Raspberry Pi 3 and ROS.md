# ROS Tutorial 001: Remotely connecting to webcam using Raspberry Pi 3 and ROS

In this tutorial I’m remotely connecting to webcam using ROS. From a laptop I’m connecting to a web camera that is connected to Raspberry Pi 3b (RPi3b for short) via USB. There is no monitor, no keyboard or mouse on Raspberry Pi 3b. I’m using SSH to connect to the Rpi3b.

## Setup
Here is the setup that I'm using.
[setup-img]
Typical USB webcamera is connected to RPi3b. RPi3b is connected to the Wi-Fi router via LAN cable (By the way, this can be replaced with a usb wi-fi adapter, so that RPi3b is connected to the router wirelessly). A PC or a laptop is connected to the router via Wi-Fi.
### Software
  * [Raspberry Pi Image from Ubiquity Robotics](https://downloads.ubiquityrobotics.com/pi.html)
  * Ubuntu 18.04 LTS (on PC)
  * ROS Melodic (on PC)
  * ROS Kinetic (on Raspberry Pi 3b)
  
### Hardware used
  * Raspberry Pi 3 b (or b+)
  * Raspberry Pi Power supply 5V, 2.2 or 2.5 A
  * LAN cable
  * WI-FI Router
  * PC or Laptop with ROS

### ROS Packages 
  * git
  * catkin_make
  * v4l-utils
  * ros-kinetic-image-view
  * net-tools
I used `git` on the PC (ROS Master) so that I could get the `usb_cam` package. However there are otherways to install it, such as `sudo apt-get install ros-kinetic-usb-cam`

The `catkin_make` should be part of ROS installation. 

Install all of these on both the PC and RPi3b:
```
cd ~/catkin-ws/src
git clone https://github.com/bosch-ros-pkg/usb_cam.git
cd ..
catkin_make
source ~/catkin-ws/devel/setup.bash
sudo apt-get install v4l-utils
sudo apt-get install ros-kinetic-image-view
sudo apt-get install net-tools
```
### Determine IP adresses of Master and Slave
Connect your Raspberry Pi to the router using LAN cable. Then find out its IP address, using ipconfig for example. 
You can checkout the details how I did it [here](Determine-IP-adresses-of-Master-and-Slave.md).
Master (PC)|Slave (Raspbery Pi 3b)
---|---
192.168.2.111|192.168.2.148

*NOTE: You IP adress would be, of course, different then mine*
### Setting up ROS Master
Actually there is nothing needs to be done here, except to start `roscore`.

### Setting up ROS Slave
Since Raspberry Pi is connected to the same network as the Master PC, we can connect to it through SSH.
```
ssh ubuntu@192.168.2.148
```
Where `ssh` takes two parameters: *[user name]@[IP-adress]*

Next, identify where is ROS Master
```
export ROS_MASTER_URI=http://192.168.2.111:11311
```
The `11311` is the default port of the ROS Master.

Identify who is ROS Slave
```
export ROS_IP=192.168.2.148
```
ROS Slave doesn’t know what he is one yet. So we must tell it the good news. In reality, this is neccessary because there may be multiple getaways available and ROS simply wouldn’t know which one to use.

## Getting video from ROS slave
Now let's get back to the ROS Master (PC) and connect to the camera.

### Via ROS Slave
Open new terminal window and connect to ROS Slave, and find out which camera adress to use:
```
ssh ubuntu@192.168.2.148
ls /dev | grep video* 
```
It’s usually `video0`, but it may differ. In fact, my RPi3b was so sensitive, that every time I moved it around (hence wiggled the webcam USB cable) it reinisiolized the adres from `video0` to `video1`. I found it a bit weird, and thought it was a software issue. But when I left it alone for an hour or so, it remained connected only to one `video0`. So I think it was just a hardware issue.

Next, modify the launch file to point to the right camera adress. In here, you can also modify the format of the output and bunch of other things. For this tutorial, the default values worked fine.
To open and edit the file, I used `nano`
```
nano ~/catkin_ws/src/usb_cam/launch/usb_cam-test.launch
```
Afterwards, launch the usb_cam package
```
roslaunch usb_cam usb_cam-test.launch
```
By the way, running this command you may see some warnings. For example, I get 
```
[ WARN] [1586989377.670962369]: unknown control 'white_balance_temperature_auto'

[ WARN] [1586989377.686331508]: unknown control 'focus_auto'
```
That simply means that the USB webcam that I use doesn't have the functionality to do automatic white balance, and auto focus (It's an old webcam with manual autofocus lens)

### Via ROS Master
That's it for the ROS Slave. Following is done in the ROS Master. Open new terminal window (source your workspace if not done so `source ~/catkin-ws/devel/setup.bash`), and verify that ROS Master sees the topics that the ROS Slave has started.
```
rostopic list
```
You should see the following:
```
  /rosout
  /rosout_agg
  /usb_cam/camera_info
  /usb_cam/image_raw
  /usb_cam/image_raw/compressed
  /usb_cam/image_raw/compressed/parameter_descriptions
  /usb_cam/image_raw/compressed/parameter_updates
```
All looks good. Next run `image-view` node to get the live video feed from the master.
```
rosrun image_view image_view image:=/usb_cam/image_raw
```
That's it. That's all. 

Here are the links that helped me through out this tutorial:

http://wiki.ros.org/usb_cam#usb_cam_node

http://roboticsweekends.blogspot.com/2017/12/how-to-use-usb-camera-with-ros-on.html

https://answers.ros.org/question/197651/how-to-install-a-driver-like-usb_cam/

https://super-geek-news.github.io/articles/414859/index.html

https://github.com/ut-ims-robotics/tutorials/wiki/Running-ROS-over-multiple-computers

[setup-img]: https://github.com/yev-d/tutos/ROS/Tutorial-001/img/setup-img.png "Setup scheme"
