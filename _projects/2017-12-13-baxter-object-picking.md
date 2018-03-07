---
layout:             project
title:              "Baxter the butler"
description:        "Baxter being trained to identify and fetch objects using voice commands and Computer vision"
keywords:           software, baxter, point cloud, cv, object recognition, grasping, manipulation, ros, robotics, control, voice command, hri, python
tags:               [Software, Baxter, Point Cloud, CV, Object recognition, Grasping, Manipulation, ROS, Robotics, Control, Voice command, HRI, Python]

folders:
  images:           "baxter-object-picking" # This path is project-dependent; don't forget to change it!

published:          true
---

## OVERVIEW
The goal of this project was to train Baxter to identify objects using a name provided by a human voice command and subsequently fetch the object when the human operator commands the robot using the previously named object.
Computer vision based perception using point clouds identify and classify the objects into different bins by their geometry/shape and then each indovidual object is further tagged with a human operator provided name. The project has two phases: an initial training phase where Baxter learns the objects via human voice commands, followed by a fetching phase where Baxter picks up objects that are asked for by the user by providing a name.

This project was implemented in ROS using the python programming language. It consisted of four software functional blocks that were implemented as separate ROS nodes: a speech processing node that leveraged the opensource software [pocketsphinx](http://www.speech.cs.cmu.edu/pocketsphinx/), a point cloud processing node that depends on the ROS packages [perception_pcl package](https://github.com/ros-perception/perception_pcl) and [openni2_camera](https://github.com/ros-drivers/openni2_camera), and a movement and grasping node that interfaces with the ROS package [moveit_python](https://github.com/mikeferguson/moveit_python).

<div style="position:relative;height:0;padding-bottom:56.25%"><iframe src="https://www.youtube.com/embed/AbHzo5lAx5A?ecver=2" width="640" height="360" frameborder="0" allow="autoplay; encrypted-media" style="position:absolute;width:100%;height:100%;left:0" allowfullscreen></iframe></div>

## DETAILED DESCRIPTION

##EXECUTIVE CONTROL (MASTER NODE)
The Master node is the co-ordinator of the whole program execution. It has three main input sources: (1) sensory data in the form of filtered point cloud data, (2) speech commands translated into integers for the state representation and strings for object names, and (3) status update messages. 

The master node keeps track of what state the demo is in based on the encoded state messages received from the speech node. Based on the current state and the incoming state, it executes the appropriate functionality. It continuously receives point cloud data from the point cloud processing node. The point cloud is stored in a sorted fashion based upon the eucledian distance from the origin. This ensures that Baxter starts picking up objects nearest to it during the learning phase. It also groups these point cloud objects together using a simple algorithm where two objects with similar height/width ratio are considered objects of the same group type, e.g. "cans". 

During the Learning phase, these objects and groups are associated with a name string provided by the user. When the object has been named, the Master node sends the location of the next object (as per euclidean distance) so that the robot arm moves to that location and picks up the object for naming. This process iterates through until all the objects are named. Baxter then enters a standby mode, returning its arm to the neutral position. 

In the Fetch phase, the Master node sends the object location to a node responsible for controlling Baxter's arm and picking up objects.

##SPEECH RECOGNITION
The speech recognition node listens for specific keywords from the user and updates Baxter's operating state accordingly. Speech recognition capability is done using PocketSphinx, Carnegie Mellon University's open source large vocabulary, speaker-independent continuous speech recognition engine.

##COMPUTER VISION
An ASUS XtionPRO LIVE is used to view Baxter's environment. Point cloud locations and the centroid of each object is published to a topic that the Master node subscribes to. This part of the project relies heavily on the perception_pcl ROS package to compute multiple point clouds of various objects on a flat surface. The a launch file reads in raw point cloud data from the XtionPRO and filters it to a more manageable dataset using the filters described below. A cluster extractor node takes the filtered point cloud data and extracts point clusters. Finally, the centroid, height, width, and width:height ratio are computed, with centroid values transformed to Baxter's frame of reference.

##KINEMATICS
Baxter's movements were controlled using the MoveIt! ROS package developed by Mike Ferguson. The node uses path planning, including collision detection, to reach the goal. The node responsible for moving Baxter receives the centroid and the object id from the Master node, uses MoveIt! to pick up the object, and goes to a predetermined joint location for lifting the object up.


##For more details, please view the [README and source code](https://github.com/HannahEmnett/InspectorBaxter) of this project on Github.
