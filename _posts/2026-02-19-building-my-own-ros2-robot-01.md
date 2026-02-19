---
title: "ROS 2 Autonomous Robot DIY Project #1:"
author: "Raymond"
date: 2026-02-19 18:00:00 +0900
categories: [Robotics, ROS2]
tags: [ROS2, ESP32, micro-ROS, DIY, Lidar]
---

## 1. Introduction: Why Build It Yourself? (Motivation)

There are many excellent autonomous robot kits on the market costing well over $1,000. However, for an engineer, the true value lies not in buying a finished product, but in understanding **"why it was built that way."**

* **Cost Efficiency**: Achieving core functionalities at 1/5 the price of commercial kits.
* **Customization**: Optimizing sensor placement and firmware to suit my specific needs.
* **Sense of Achievement**: The thrill of seeing a robot move using the very screws I sourced from local hardware alleys in freezing -10°C weather.

![Commercial robot platform for benchmarking](../assets/images/vscodeimages/img_2026-02-19-182127.png)
---

## 2. Choosing the Brain: ESP32 and micro-ROS

For the robot's "heart," I chose an **ESP32 custom board**. Based on my 30 years in IT, the choice of ESP32 over STM32 was clear:

* **Official micro-ROS Support**: It offers seamless integration with micro-ROS, unlike the more complex setup required for STM32.
* **Connectivity**: Built-in Wi-Fi and Bluetooth allow for effortless wireless data exchange with my VMware host.
* **Expandability**: Dedicated ports for Lidar and 4-axis encoder motors are a huge plus.

> **Technical Tip**: I use a pre-configured VMware image for the development environment. It comes with ESP-IDF and ROS 2 Humble (including Gazebo and Rviz2), drastically reducing setup time.

![Hardware layout of the selected ESP32 board](../assets/images/vscodeimages/img_2026-02-19-182150.png)
---

## 3. Chassis and Drivetrain: Aluminum Rigidity and 310 Motors

I selected an **aluminum-based chassis** for the robot's frame, as structural rigidity directly translates to data precision.

* **310 Encoder Motors**: These provide precise feedback on wheel rotations, minimizing errors during SLAM (Simultaneous Localization and Mapping).
* **Multi-tiered Structure (Base Plate)**: I designed an additional upper plate using **Fusion 360**, to be laser-cut from acrylic or 3D printed. This ensures an unobstructed field of view for the Lidar and Camera.

![Assembly of the aluminum chassis and 310 encoder motors](../assets/images/vscodeimages/img_2026-02-19-182213.png)
---

## 4. Software Architecture and Final Goals

If the hardware is the body, the software is the intelligence.

1. **Hybrid Control**: Performing ROS 2 tests using a Raspberry Pi or a remote micro-ROS Agent on VMware.
2. **AI Collaboration (Gemini)**: Porting the T-mini Lidar driver by collaborating with Google's Gemini AI to analyze hardware specs and protocols.
3. **The Ultimate Goal**: Completing a SLAM map and developing a system that can execute navigation commands autonomously.

![SLAM visualization in RViz2 and autonomous navigation example](../assets/images/vscodeimages/img_2026-02-19-182444.png)
---

## Closing Thoughts

This journey—understanding communication protocols, porting drivers, and building an intelligent system from scratch—is the true essence of DIY robotics. In the next post, I'll share the "Battle of the Screws" episode from the freezing streets of the local tool district and the beginning of the actual assembly.