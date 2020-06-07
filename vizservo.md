---
layout: page
title: "Visual Servo Control"
mathjax: true
permalink: /vizservo/
---

Table of Contents:

- [Introduction](#intro)
- [Summary](#summary)

### What is it?

Visual servo control, also commonly referred to as **Visual Servoing** (VS), is a technique that uses vision data (i.e. a camera) to control the motion of a robot. We can use visual servoing to close the loop around any task that combines visual sensing and manipulation. Such systems may be off because they do not necessarily have fully accurate camera calibration or position control. The environment may also be dynamic. We call this imprecision in sensing and control. To increase the accuracy of the system, we can use visual-feedback to provide closed-loop position control for a robot end-effector.

There are usually two types of VS configurations:

- **eye-in-hand**: here, the camera is mounted directly on the robot and so motion of the robot incudes motion of the camera.
- **eye-to-hand**: here, the camera is fixed somewhere in the world and observes the motion of the robot.

There are two common types of VS techniques:

- **position-based** VS:
- **image-based** VS:

In this tutorial, we'll develop intuition of visual servoing by considering an eye-in-hand configuration.

### Why Visual Servoing?

Visual servoing presents several advantages:

* can adapt to dynamic environments
* does not necessarily* need fully-accurate camera calibration or position control

Why the caveat? Well there are 2 kinds of VS, position-based and image-based. Image-based VS is the one whose performance is independent of calibration. This follows from the fact that by construction, when the image error function is zero, the kinematic error must also be zero. Even if the hand-eye system is miscalibrated, if the feedback system is asymptotically stable, the image error will tend to zero and hence so will the kinematic error.

<a name='intro'></a>

### Introduction

Imagine we've build a little robot to clean up trash and debris from a beach.

Steps for derivation:

* The robot has a grasping algorithms that looks at a scene and gives us the image coordinates of the object to grasp as well as grasping angle. We simplify our approach to top-down grasping.
*



### Examples in Papers

In this section, I want to try and compare classical visual servoing with "closed-loop" variants introduced in the last 2 years in deep learning research. In particular, I'll be taking a look at 2 papers:

* http://proceedings.mlr.press/v78/viereck17a/viereck17a.pdf
* https://arxiv.org/pdf/1804.05172.pdf

In GGCNN, the authors implement a PBVS system where a CNN is used to generate a top down grasp given a depth image at a rate of 30 Hz. The CNN computes a dense grasp map so the authors need to select the best grasp at every time step. To avoid rapidly switching between similarly-ranked grassps (which would confuse the controller), they choose the max grasp that is most similar to the previous one in image space. They generate a 6D velocity signal for the end-effector using the difference between the predicted final grasp pose and the current end-effector pose, scaled by a lambda parameter. The lambda parameter is probably the equivalent of a step-size, we don't want to take a huge step towards the desired goal pose.

In the Viereck paper, they don't really do velocity control. Rather, they run the control loop and move by a constant fractional step-size in the direction of the nearest grasp. Since they have constrained their grasping to top-down, it basically boils down to taking a step size in the z direction and stopping when they get close enough to the object. So I guess this is just an over-simplified position based controller.


<a name='summary'></a>

### Summary


### References

- [A Tutorial on Visual Servo Control](http://www.cs.jhu.edu/~hager/Public/Publications/TutorialTRA96.pdf)
- [Visual Servo Control Part I: Basic Approaches](https://hal.inria.fr/inria-00350283/document)