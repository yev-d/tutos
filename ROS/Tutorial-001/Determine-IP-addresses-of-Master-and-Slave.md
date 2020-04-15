## The goal 

Connect to Raspberry Pi 3b (RPi3b for short) 

## Environment 

  * Ubuntu 18.04 LTS on PC 

  * Ubiquity Robotics Raspberry Pi Image on RPi3b 

## Workflow 
  * Connect RPI3b to Wi-Fi router via LAN 
  * Connect to RPi3b via Wi-Fi 
  * Get RPi3b IP-address 
  * Disconnect from RPi3b and connect to Wi-Fi router 
  * Connect to RPi3b via SSH 

## Step 1: Connect RPI3b to Wi-Fi router via LAN cable

## Step 2: Connect to RPi3b via Wi-Fi 

Raspberry Pi Image from Ubiquity Robotics comes with pre-configured Wi-Fi hotspot under the name *ubiquityrobot####* 

<img src=img/step2.png width="30%" height="30%" />

Select it and connect to it using **ubuntu** as a password. 

<img src=img/step22.png width="30%" height="30%"/>

## Step 3: Get RPi3b IP-address 

Open new terminal window and connect to RPi3b using this SSH login and address with this password **ubuntu**: 

``` 

ssh ubuntu@10.42.0.1 

``` 

Then get the IP-address using `ifconfig`.  

## Step 4: Disconnect from ubiquityrobot#### Wi-Fi and connect to your Wi-Fi router 

## Step 5: Connect to RPi3b via SSH 

Open new terminal window and connect to RPi3b using SSH with the IP-address determined in step 3. The password is the same. 

``` 

ssh ubuntu@192.168.2.### 

``` 

Now Raspberry Pi 3b can be controlled from your PC, and you don’t even need any additional keyboard or monitor. 

## References
https://downloads.ubiquityrobotics.com/pi.html
 
