---
layout:             project
title:              "Feedforward & PI controller for a Mobile Robotic Arm"
description:        "Implemented a Feedforward & PI controller for making the end-effector of a Kuka mobile robotic arm follow a pre-defined trajectory"
keywords:           software, simulation, python, robotics, mobile robots, controls, controller, trajectory follower, kuka, youbot
tags:               [Software, Simulation, Python, Robotics, Controls]

folders:
  images:           "mobile-robot-sim" 

published:          true
---

This is the final project for the [Robotic Manipulation class](http://hades.mech.northwestern.edu/index.php/ME_449_Robotic_Manipulation) taught by [Dr. Kevin Lynch](http://www.mccormick.northwestern.edu/research-faculty/directory/profiles/lynch-kevin.html). This project exercised the application of knowledge gained in this class through the entire quarter. The goal of this project was to develop a feedforward and PI controller simulation for a Kuka YouBot running in Coppelia Robotics' **[VREP](http://www.coppeliarobotics.com/)**.

Kinematics
Initial conditions for the [Kuka YouBot](http://www.youbot-store.com/developers/) (position, orientation, and arm joint angles) and a pre-defined end-effector trajectory over a fixed timespan were specified. The desired trajectory is given as a position and an orientation that change over time. At each point along the path, the controller solves for both the joint angles of the 5 joint robot arm and the wheel rotations of the base that will place the end effector at that desired point. I used odometry to gauge and control the position and orientation of the mechanum-wheeled chassis. The deviation or error between the desired path and the current path is fed into the combined feedforward-PI controller to compute inputs for the next time step. The controller and all associated libraries were written entirely in Python.

<iframe width="560" height="315" src="https://www.youtube.com/embed/hlaINY93YOM" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>
