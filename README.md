# jetson-jessicar
Reference link : https://zeta7.notion.site/zeta7/JessiCar-1449b3fd5c984bab816920cb2b92002d

## ROS (Robot Operating System)
ROS is an open source robot operating system for robot operation.
It provides a wealth of tools and libraries for developing and running various robots and robot applications.

Modularity and transparency: It is modular, consisting of multiple nodes, and nodes can communicate transparently with each other.
Message communication: Nodes communicate using message types defined in ROS. This makes it easier to exchange data.
Package Systems: It uses a package system to organize and share code, compiled executable files, data, libraries, and more.
Simulation and visualization: The abundance of tools to simulate and visualize robots makes development and testing easier.

Topic: "Topic" is the mechanisms by which messages are exchanged between nodes. "Publish" a message about a particular topic, and another node "subscribes" to that topic to receive the message. Topics support asynchronous communication, and nodes perform specific functions by subscribing only to topics they are interested in.
Service: "Service" is a synchronous communication mechanism consisting of "request" and "response". When a client node requests a service, the server node responds. The service is usually used when one-off work is required.
Action: Similar to the "service", but used for long-running tasks. Clients send goals, and servers send interim results periodically until the task is complete.

rqt_graph: As one of the GUI tools provided by ROS, it is a tool that visually displays graphs between nodes and topics in the currently running ROS system. This makes it easy to understand and debug the ROS system's "topic subscription" and "publication" relationships, and connections between nodes.

## YOLO (You only live once)
YOLO is deep learning algorithm for Object Detection.
It divides images into several grids with only one forward pass and predicts bounding boxes and class probabilities for objects in each grid.
It is effectively used for real-time object detection.

Real-time processing: It can perform object detection with only one forward pass, so it can be executed in real time.
Grid Systems: It divides images into grids and predict which objects each grid cell belongs to.
Various Object Detection: You can detect bounding boxes and classes for multiple objects in one image.
Minimize duplicate detection of objects: When an object spans multiple grid cells, It creates only one bounding box to minimize duplicate detection.

## Darknet_ros
Darknet_ros is a ROS package for integrating the Darknet framework with the ROS.
This package uses Darknet to perform real-time object detection, publishing the results as ROS messages, and communicating with other ROS nodes.

Real-time Object Detection: It uses the "YOLO" algorithm to detect multiple objects in real time.
Integration with ROS: It converts Darknet results to ROS messages and communicate with other ROS nodes through ROS topics.
Set parameters: The user can set the desired object detection behavior by adjusting the parameters in darknet_ros.
Visualization: It supports RViz(Robot Visualization) messages for visualizing results.

*RViz: It is used to visually represent robot and sensor data.

# Circuit Diagram
<img width="589" alt="image" src="https://github.com/data3rr0r/jetson-jessicar/assets/102942399/36c1cff6-2fb4-46c8-9dcb-95989b85acc3">



# Software setup
## Ubuntu Installing/Basic Setup
Assumes you have a monitor/keyboard/mouse for jetson.
Reference : https://zeta7.notion.site/2-Jetpack-ROS-install-ddf19c38e55d4166a559ad803b2f2a4e
Install image downloaded from this link : https://drive.google.com/file/d/1HU5F1cwiw2wzuNBdLL9R3Wvpg5AXLzw5/view?usp=sharing 
After flashing, login with following credentials.
* ID: jetson
* PW: jetson
Connect to the wifi (wifi dongle required) or ethernet.
Install jetson fan control script with the following code. Skip this if you don't have it.
```
cd Downloads
git clone https://github.com/jetsonworld/jetson-fan-ctl.git
cd jetson-fan-ctl

sudo sh install.sh
```
Check if I2C connection is established with `sudo i2cdetect -r -y 1` command. Address may be different depending on board.
## Installing ROS
Execute the following codes.
```
cd ~/Downloads/
sudo apt update

git clone https://github.com/zeta0707/installROS.git
cd installROS
./install-ros.sh
```
To improve environment. add the following lines to ./.bashrc
```
alias cma='catkin_make -DCATKIN_WHITELIST_PACKAGES=""'
alias cop='catkin_make --only-pkg-with-deps'
alias sds='source devel/setup.bash'
alias coc='catkin clean'
alias cca='catkin clean -y'

# If using mirco USB cable connection, jetson master
export ROS_MASTER_URI=http://192.168.55.1:11311
export ROS_IP=192.168.55.1

source /opt/ros/melodic/setup.bash
source ~/catkin_ws/devel/setup.bas
```
Be sure to update the terminal after editing by `source ~/.bashrc' command

Install catkin workspace. Execute `cca` then `cma`

Check if ROS is working or not. (Optional)
Open 3 terminals and execute following commands.
```
# Execute following command on Terminal 1
cd catkin_ws
roscore
# Execute following command on Terminal 2
cd catkin_ws
rosrun turtlesim turtlesim_node
# Execute following command on Terminal 3
cd catkin_ws
rosrun turtlesim turtle_teleop_key
```
## Installing jessicar code/config settings
Execute the following command.
```
cd ~/catkin_ws/src
git clone https://github.com/zeta0707/jessicar.git
cd ~/catkin_ws
```
I assume you use image with OpenCV downgraded to 3.4.x. The image provided in this document has OpenCV 3.4.6 installed on `/usr/local` Execute the following codes to make building cv_bridge possible.
```
cd ~/Downloads/opencvDownTo34
sudo patch -p1 /opt/ros/melodic/share/cv_bridge/cmake/cv_bridgeConfig.cmake -p1 < cv_brige.patch
cd ~/catkin_ws
cma
```
To create config files for your device, execute the following command.
```
# command usage
Usage: src/jessicar/script/jetRccarParam.sh target
target: select one among these
jetracer, jetbot, motorhat2wheel, motorhatSteer, nuriBldc, 298n2Wheel, pca9685Steer

# example (for jetracer)
$ cd ~/catkin_ws/src/jessicar/script
$ ./jetRccarParam.sh jetracer
```
If you are using USB camera, execute the following commands too.
```
$ cd ~/catkin_ws/src/jessicar/script
$ ./camSelect.sh usbcam
```

Now, install jessicar with `sudo apt install ros-melodic-desktop-full` command. You may need to execute `sudo apt install ros-melodic-joy* \ ros-melodic-teleop-twist-joy ros-melodic-teleop-twist-keyboard \ python-smbus ros-melodic-ackermann-msgs ros-melodic-web-video-server \ ros-melodic-image-pipeline python-pip` to install other ROS packages.

## Testing keyboard input
Open 2 terminals and execute the following commands.
```
# terminal #1
$ roslaunch jessicar_control keyboard_control.launch

# terminal #2
$ roslaunch jessicar_teleop jessicar_teleop_key.launch
```
key mapping is as follows:
w/x : increase/decrease linear velocity
a/d : increase/decrease angular velocity
space key, s : force stop

![image](https://github.com/data3rr0r/jetson-jessicar/assets/102942399/4231fad4-b8a0-46da-94d9-6d319a740d17)
This is NOT hold, just toggle.

## Testing Camera 
Install ROS melodic image view by `sudo apt install ros-melodic-image-view` command.
Open 2 terminals and execute the following. 
```
# Terminal 1
$ roslaunch jessicar_camera csicam.launch

# Terminal 2
rqt_image_view
```
a new popup will appear. check if you get camera input.

## Blob Tracking
Connect Jetson to monitor and prepare keyboard and mouse. Run the following commands LOCALLY.
```
# terminal #1
$ roslaunch jessicar_control blob_all.launch

# Execute image_view to check image, DISPLAY required
$ rosrun image_view image_view image:=/blob/image_blob
# OR
$ rqt_image_view
```
and check if it detects green objectives.

![image](https://github.com/data3rr0r/jetson-jessicar/assets/102942399/bc5a21f0-8063-48e2-b228-9a49d6c826c7)

Below link is my video testing if blob detection is working fine.
https://www.youtube.com/watch?v=nPi-mzWyYeY 


## Yolo4-tiny and darknet_ros 
Install darknet_ros with the following:
```
sudo apt-get install -y ros-melodic-image-pipeline

cd ~/catkin_ws/src
git clone --recursive https://github.com/Tossy0423/yolov4-for-darknet_ros.git
```
I deceded to use Tossy0423's repository since it supports YOLO4. You can use others as well if they support YOLO4.
We need some modifications for darknet_ros. Use the following commands.
```
cd ~/catkin_ws/src/yolov4-for-darknet_ros/darknet_ros
git clone https://github.com/zeta0707/darknet_ros_custom.git
cp -rf darknet_ros_custom/* darknet_ros/
```
This will modify yolov4 to yolov4-tiny, and apply config and weights for custom datasets.
Now we have packages ready. build with 'cma' command.
# Yolov4 tiny with my custom traffic sign
We will use 2 terminals. The car will do some operation (move/stop/turn right/turn left/etc..) based on camera input. I used pretrained dataset, but it can be created also.
Open 2 terminals and execute the following respectively.
```
#terminal #1, #object detect using Yolo_v4
roslaunch darknet_ros yolov4_jessicar.launch

#terminal #2,camera publish, object -> start or stop
roslaunch jessicar_control traffic_all.launch
```

This is my demo video. Check this out, and watch how the front/back wheels of the car operates based on each signs. 
Link: https://youtu.be/IFFw-60XPq4
