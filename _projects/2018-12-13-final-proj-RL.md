---
layout:             project
title:              "Reinforcement Learning"
description:        "Reinforcement Learning of control policies for Robotic manipulation"
keywords:           software, Reinforcement Learning, Policy Gradient, Convolution Neural Networks, CNN, Deep Learning, Q Learning, OpenAI, Gym, MuJoCo, Sawyer, Object recognition, Grasping, Manipulation, ROS, Robotics, Optimal Control, Python
tags:               [Software, Reinforcement Learning, Policy Gradient, Convolution Neural Networks, CNN, Deep Learning, Q Learning, OpenAI, Gym, MuJoCo, Sawyer, Object recognition, Grasping, Manipulation, ROS, Robotics, Optimal Control, Python]

folders:
  images:           "final-project" # This path is project-dependent; don't forget to change it!

published:          true
---

## Introduction
Robots can perform impressive tasks including surgery and assisting in manufacturing.  However, for a robot to perform these tasks, a human operator has to either manually operate the robot or the robot follows a specific pre-programmed algorithm in order to perform the required task. Both these approaches are limited in that they do not enable learning new tasks in an autonomous fashion. Reinforcement learning holds the promise of enabling a robot with learning skills required for autonomous operation. However, this is is still an emerging area of research and major challenges remain, even for learning tasks that are simple for a 2 year old to learn.

## Project Synopsis
My MSR Capstone project is to enable Sawyer robot to learn the task of inserting a solid block into a shape sorting cube.
Policy Gradient, a popular Reinforcement Learning algorithm, is used to learn the policy which takes as input observations of the environment (i.e. robot joint angles and joint velocities, position of object in robot's gripper and location of shape sorting cube), and outputs control actions in continuous domain as either joint torques or velocities.
My implementation uses the same interface between the Policy Gradient agent and the Sawyer ROS environment as the interface in OpenAI's Gym simulated envoronments. Several different combinations of hyper-parameters were searched to find the optimal tradeoff between policy accuracy and training time. Multiple goal locations in cartesian space were used during training. During testing, the learnt policy was able to guide Sawyer to these goals within the accuracy threshold used during the training phase.

For more information on the Policy gradient algorithm please see the [online lecture notes for the CS294 class](http://rail.eecs.berkeley.edu/deeprlcourse/static/slides/lec-5.pdf) taught at UC Berkeley. I am very thankful to Prof. Sergey Levine for making this class available online to non-UC Berkeley students.

[Please see the github repo for details of the software implementation](https://github.com/srikanth-kilaru/rl-projects/tree/master/pg)

<div style="position:relative;height:0;padding-bottom:56.25%"><iframe src="https://www.youtube.com/embed/FWMsvvGFMtU?ecver=2" width="640" height="360" frameborder="0" allow="autoplay; encrypted-media" style="position:absolute;width:100%;height:100%;left:0" allowfullscreen></iframe></div>

<div style="position:relative;height:0;padding-bottom:56.25%"><iframe src="https://www.youtube.com/embed/_a9Cef3pkJk?ecver=2" width="640" height="360" frameborder="0" allow="autoplay; encrypted-media" style="position:absolute;width:100%;height:100%;left:0" allowfullscreen></iframe></div>

[![](http://img.youtube.com/vi/FWMsvvGFMtU/0.jpg)](http://www.youtube.com/watch?v=FWMsvvGFMtU "Sawyer implementing Policy Gradient")

[![](http://img.youtube.com/vi/_a9Cef3pkJk/0.jpg)](http://www.youtube.com/watch?v=_a9Cef3pkJk "Sawyer implementing Policy Gradient")


## Experiment details
Sawyer's task is to insert a green cylinder into a circular hole on a shape sorting cube.
Sawyer's joints right_j3, right_j4, right_j5 were controlled by the policy while other joints were not controlled and maintained in fixed configurations purely for space constraint reasons and for the purpose of avoiding collisions with the environment during training phase.

### Goal Specifications
Two goals were specified in Sawyer's workspace. Goals can be specfied as joint angles or cartesian coordinates. If goals are specified as angles, PyKDL along with object geometry is used to transform the goal angles into a goal in cartesian space. During training, specified goals are iterated through in a round robin fashion with a small amount of gaussian noise added to the goal location.

### Initial conditions
At the beginning of each trajectory, along with the selection of a new goal, the joints return to a region contained by a gaussian around the fixed and pre-defined initial joint angles

### Reward  function
Multiple versions of reward functions were tried in the early stages of development and the following function was finally settled upon as it had the best performance

Reward = w_l2 \* l2\*\*2 + w_log \* log(l2\*\*2 + α) + w_u \* \|\|u\|\|\*\*2

where l2 is the Eucledian distance between the center of the cyclinder's circular face towards the shape sorting cube and a point at a fixed height above the center of the circular hole in the shape sorting cube. w_l2 was chosen as 1e-3.

The reward function has the log of the l2 squared distance term to produce higher rewards as the distance between the points becomes closer. alpha was specified as 1e-6, and w_log was chosen as 1.0.

u is the control output vector and w_u was chosen as 1e-2.

### Hyperparameter search
Both reward to go and advantage normalization options were enabled
A maximum trajectory length of 800 and total timesteps of 4800 between each policy iteration. With this configuration, each policy iteration took around 9 minutes and a total of ~16 hours to train over 100 policy iterations. Increasing these quantities increases the training time and did not necessarily produce a better policy.

A reward discount factor of 0.9 and a learning rate of 0.005 worked well.

The policy network consisted of two layers and 64 tanh activation units and the output layer was made of linear activations and the dimension of output, i.e. dimensions of control action vector

### Observations (Input to policy)
For velcoity control mode

[angle_0, angle_1, ...., angle_N, x_object, y_object, z_object, x_goal, y_goal, z_goal]

For torque control mode

[angle_0, angle_1, ...., angle_N, vel_0, vel_1, ..., vel_N, x_object, y_object, z_object, x_goal, y_goal, z_goal]

where N is the number of joints being controlled

## Actions (Output of policy)
For velcoity control mode

[vel_0, vel_1, ...., vel_N]

For torque control mode

[torque_0, torque_1, ..., torque_N]

where N is the number of joints being controlled

## Learning resources
- UC Berkeley course [CS 294: Deep Reinforcement Learning, Fall 2018](http://rail.eecs.berkeley.edu/deeprlcourse/)
- [RL course by David Silver](https://www.youtube.com/watch?v=2pWv7GOvuf0&app=desktop)
- [Deep RL bootcamp](https://sites.google.com/view/deep-rl-bootcamp/lectures)
- [Stanford class CS231n](http://cs231n.stanford.edu/). See the 2017 lectures if you would like to see video.
- [Reinforcement learning foundations at Northwestern University](https://jermwatt.github.io/mlrefined/)


### Simulation software for testing RL algorithms
- [Programming in MuJoCo](http://www.mujoco.org/book/programming.html)
- [OpenAI Gym simulation](https://openai.com/) environment for RL algorithms
