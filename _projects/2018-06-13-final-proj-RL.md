---
layout:             project
title:              "Reinforcement Learning"
description:        "Reinforcement Learning of control policies for Robotic manipulation and grasping"
keywords:           software, Reinforcement Learning, Convolution Neural Networks, CNN, Deep Learning, Q Learning, OpenAI, Gym, MuJoCo, Baxter, Object recognition, Grasping, Manipulation, ROS, Robotics, Optimal Control, Python
tags:               [Software, Reinforcement Learning, Convolution Neural Networks, CNN, Deep Learning, Q Learning, OpenAI, Gym, MuJoCo, Baxter, Object recognition, Grasping, Manipulation, ROS, Robotics, Optimal Control, Python]

folders:
  images:           "final-project" # This path is project-dependent; don't forget to change it!

published:          true
---

## Overview
Robots can perform impressive tasks including surgery and household chores.  However, for a robot to perform these tasks, a human operator has to either manually operate the robot or the robot follows a specific pre-programmed control path in order to perform the required task. Both these approaches are limited in that they do not scale for a robot to perform a task in an autonomous fashion in an unstructured and previously unseen environment. 

Reinforcement learning holds the promise of enabling a robot with perception and control for autonomous operation. However this is is still an emerging area of research and major challenges remain, even for basic tasks. I have listed some resources for the reader to acquaint themselves about Reinforcement Learning and links to some research publications on it's application to Robotics. 
A well known method used by Reinforcement Learning algorithms is the Policy search method, and this method holds the promise of allowing robots to automatically learn new behaviors through experience and can allow robots to learn control policies for a wide range of tasks. But practical applications of policy search often require hand-engineered components for perception, state estimation, and low-level control.

My final MSR project, a Capstone project that is individually accomplished under an advisor much like a MS Thesis in some programs, is to develop the software that implements the algorithm developed by members of the Robotic Artificial Intelligence and Learning Lab at UC Berkeley.



## Project Outcomes
The main goal of my MSR final project would be to write software to implement and test the algorithm outlined in this Berkeley paper in both the MuJoCo simulation environment (first) and  with the Baxter robot in a real environment. 
### MuJoCo simulation
The simulation experiment in MuJoCo would implement the 
2D peg insertion task with 6 state dimensions and 2 action dimensions
3D version of this task has 12 state dimensions and 2 action dimensions

### Baxter experiment
Baxter will perform the shape sorting cube task. This requires the robot to insert a red trapezoid into a trapezoidal hole on a shape sorting cube. During training, the cube will be positioned at nine different positions, situated at the corners, edges, and middle of a rectangular region 16 cm × 10 cm in size. During training, the shape sorting cube will be moved through the training positions by using the left arm. A trial will be considered successful if the bottom face of the trapezoid is completely inside the shape sorting cube, such that if the robot were to release the trapezoid, it will fall inside the cube.

### Project Dependencies
- A GPU enabled machine for running the learning algorithms. The initial attempt would be to use a Google cloud Linux VM with 4-8 GPUs (P100  or greater). I have this setup already up and running on GCP. If the network latency and jitter experienced by the communication between the local lab camera and the GCP VM turns out to be a problem, we have to find a local GPU compute resource. 
- Caffe2 software. This algorithm uses CNN and Caffe is a very popular framework to implement CNNs especially as it is optimized for GPUs. I have Caffe2 installed on my GCP VM and I have successfully run training and inference using Facebook’s open source CNN algorithm code named Detectron (Fast RCNN with FPN and RPN) using the COCO image dataset. I may end up switching to a different, and possibly much simpler CNN, to closely align with the Berkeley paper’s CNN choice. Regardless, I feel like I am well equipped for this particular aspect of my task.
- MuJoCo – I have this software licensed and installed on my linux laptop and working. I am in the learning mode for using and developing in MuJuCo, as of this writing (Version 1).
- Baxter and ROS – I have used Baxter in my previous projects and it is available in the lab. I will need to figure out if using both arms of Baxter prove to be a challenge, from a collision perspective, when one arm is doing peg insertion and the other arm is doing the block moving.
- Shape sorting cube – Last but not least, the shape sorting cube and the geometrical pieces like the trapezoid that fit into it are the objects of robotic manipulation. If these are not available for ready-made purchase, I will build this using a 3D printer and/or laser cutter in the MSR lab. 



##### For more details on the software implementation, please view the [README and source code](https://github.com/srikanth-kilaru/final_proj) of this project on Github.
