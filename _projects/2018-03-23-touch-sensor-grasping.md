---
layout:             project
title:              "Touch sensor assisted grasping"
description:        "Touch sensors on Sawyer's grippers assist 3D Point Cloud data to refine localization and grasping of objects"
keywords:           software, sawyer, sensors, IR, infrared, touch, point cloud, cv, object recognition, grasping, manipulation, ros, robotics, control, hri, python
tags:               [Software, Sawyer, Sensors, IR, Infrared, Point Cloud, CV, Object recognition, Grasping, Manipulation, ROS, Robotics, Control, Voice command, HRI, Python]

folders:
  images:           "sawyer-touch-grasping" # This path is project-dependent; don't forget to change it!

published:          true
---

## Overview

Robots rely on Computer Vision technologies like depth sensing or point cloud information obtained from RBGD cameras to perceive the size and location of objects they have to grasp and manipulate. However many challenges remain in perceiving accurate location and size of the objects due to factors like unfavorable lighting conditions, occlusions, noisy data, objects not being perfectly still etc.

Humans rely on their sense of touch to perform fine-grained localization and grasping of an object once their hands reach a location based on coarse grained information provided by their eyesight.

The goal of this project is to use a similar approach in localizing and grasping an object once the robot is commanded to move its end-effector (gripper) to the object's location provided by the 3D point cloud data.

To this end, I used touch sensors from [Robotic Materials](http://roboticmaterials.com/rm/product/smart-gripper-pads-for-robotiq/) that were mounted on both the gripper fingers. I used the [ASUS XtionPRO LIVE](https://www.asus.com/us/3D-Sensor/Xtion_PRO_LIVE/) for vision sensing.

<div style="position:relative;height:0;padding-bottom:56.25%"><iframe src="https://www.youtube.com/embed/AbHzo5lAx5A?ecver=2" width="640" height="360" frameborder="0" allow="autoplay; encrypted-media" style="position:absolute;width:100%;height:100%;left:0" allowfullscreen></iframe></div>

This project was implemented in ROS using the python programming language and it consists of three main software functional blocks:
1. A sensor reading node from [Robotic Materials](https://github.com/RoboticMaterials/finger-sensors-ros) that reads touch sensor data coming in through a USB port and publishes this data on ROS topics '/left_finger/sai' and '/right_finger/sai' software.
2. A point cloud processing node that depends on the ROS packages [perception_pcl](https://github.com/ros-perception/perception_pcl) and [openni2_camera](https://github.com/ros-drivers/openni2_camera).
3. A touch sensing and grasping node that interfaces with the ROS package [intera_sdk](https://github.com/RethinkRobotics/intera_sdk/) to control Sawyer's end effector position and for closing / opening the gripper.


## DETAILED DESCRIPTION

### Touch Sensors
Each of the two fingers of Sawyer's grippers is attached to a touch sensor pad. Each sensor pad has a total of eight sensors, 4 on the inner surface and two on the outer surface as shown in the pictures below -

![RM Image of Sensor pad](assets/images/projects/sawyer-touch-grasping/robotiq_drawing_sensor_num.png)
![Closeup Image of Sensor pad](assets/images/projects/sawyer-touch-grasping/finger_closeup.jpg)

Each of the eight touch sensors on each of the two fingers has a different sensitivity and noise profiles and therefore has to be calibrated individually to detect whether it has touched an object or not. In addition the concept of touch is not a binary notion since the sensor values goes up as the object comes closer to the sensor even before a complete contact is made.
See the picture below to get an idea of the different levels of sensor values and noise levels for the two fingers without any object in contact.
![Image of sensor noise](assets/images/projects/sawyer-touch-grasping/Sensor-graphics.png)

### Computer Vision
An ASUS XtionPRO LIVE is used to view objects that need to be grasped. Point cloud location and the centroid of the object to be grasped is published to a topic that the touch_sense grasping node subscribes to. This part of the project relies heavily on the perception_pcl ROS package to compute point cloud of the object on a flat surface. The launch file reads in raw point cloud data from the XtionPRO and filters it to a more manageable dataset using filters. A cluster extractor node takes the filtered point cloud data and extracts point clusters. Finally, the centroid, height, width, and width:height ratio are computed, with centroid values transformed to Sawyer's frame of reference.
![Image of pcl](assets/images/projects/sawyer-touch-grasping/point_clouds.png)

### Touch Sensing and Grasping node (The Main Node)
This is the main node where the touch sense based localization and grasping logic is implemented.
This node subscribes to ROS topics that publish the sensory data coming in from the sensor reading node and the pointcloud information (PCL data) coming in from the computer vision node.
The PCL data gives the centroid location of the object to be grasped and this is the location in Cartesian coordinates of Sawyer's base frame is where the gripper is commanded to move to. Then the grippers close in small increments, both to prevent deforming delicate objects as well as notice any touch contact. If there is partial contact on any of the sensors (either the inner or the outer), the end effector and the gripper move in such a way as to deepen the grasp. If there is zero contact at the initially commanded location, the end effector is commanded to the next search location within close proximity of the initial location and it repeats the whole search process by slow closing of the grippers. In this way the gripper iterates through a series of search locations in and around the location initially identified as the object's centroid location by the PCL data.
### For more details on the implementation of the search algorithm, please view the [README and source code](https://github.com/srikanth-kilaru/winter_proj) of this project on Github.

### Kinematics
Sawyer's movements were controlled using the intera_sdk ROS package developed by [Rethink Robotics](http://sdk.rethinkrobotics.com/intera/Main_Page). Specifically the [intera_motion_interface](http://sdk.rethinkrobotics.com/intera/Robot_Interface) from this package is used by the touch_sensing node to command Sawyer to take the end effector to a specific cartesian location and to open / close the grippers. The intera_motion_interface does the path planning, including collision detection, to reach the goal.


