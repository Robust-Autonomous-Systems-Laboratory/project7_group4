# Project 7: Introduction to the ROS2 Navigation Stack

## Team Members
- Eineje Victor Ameh
- Reid Beckes

---

## Introduction and Setup

This project introduced the ROS 2 Navigation Stack (Nav2) through simulated and real-world robot navigation tasks. The overall goal was to understand how a mobile robot localizes itself, builds or uses a map, plans a path, avoids obstacles, and navigates to a goal.

For this project, we used **ROS 2 Jazzy** on **Ubuntu 24.04**. We worked with the **TurtleBot3 Burger** in both simulation and on physical hardware. For the real robot portion, we connected to the lab Wi-Fi setup and used remote ROS communication between the laptop and the robot.

### Setup Challenges and Resolutions

One challenge was package setup for TurtleBot3 simulation and navigation. We resolved this by installing the required ROS 2 packages, cloning the TurtleBot3 repositories into a workspace, and sourcing the correct setup files before launching nodes.

Another challenge was connecting to the physical TurtleBot3 from the laptop. We resolved this by connecting to the `ee3280_roslab` network, using a `turtlebot_connect.sh` script with the correct `ROS_DOMAIN_ID`, and verifying the robot topics with `ros2 topic list`.

A third challenge was Git and GitHub setup. We had to fix the repository setup, configure Git identity, and switch from HTTPS to SSH authentication so we could push changes successfully.

---

## Part 1 — TurtleBot3 Simulation

For Part 1, we launched the TurtleBot3 Burger simulation in Gazebo and started the Nav2 stack in a second terminal. After launching RViz2, we used the **2D Pose Estimate** tool to initialize AMCL and then sent multiple navigation goals using the **Nav2 Goal** tool.

### What We Observed

We observed that Nav2 generated a global path from the robot’s pose to the selected goal and continuously updated the robot’s local behavior through the local costmap. As the robot moved, RViz showed the path, obstacle inflation areas, and the robot’s current estimated pose. We also observed brief recovery or safety-related behavior in the logs, including collision-monitor warnings, but the robot still successfully reached the commanded goal.

This helped us understand the relationship between:
- localization through AMCL
- global path planning
- local trajectory control
- costmap-based obstacle avoidance

### Part 1 Evidence

#### Part 1 RViz Navigation View
![Part 1 RViz Navigation View](figures/goal1-rviz.png)

#### Part 1 Terminal Goal Confirmation
![Part 1 Terminal Goal Confirmation](figures/goal1-term.png)

---

## Part 2 — Real-World Mapping of EERC 722

For Part 2, we used the physical TurtleBot3 Burger and connected to it remotely from the laptop. We first connected the laptop to the lab robot network and used a TurtleBot connection script so the laptop could access the robot topics. Then we SSHed into the TurtleBot3 and launched the onboard bringup node. On the laptop, we launched Cartographer and RViz2, then started keyboard teleoperation in a separate terminal.

### Mapping Strategy

We drove the TurtleBot3 slowly around the perimeter of the room first so the outer wall structure would appear clearly in the map. After that, we moved through interior areas to capture more features and open space. We tried to keep motion smooth and mostly linear, and used in-place turns instead of wide arcs whenever possible to reduce odometry-related distortion.

### Mapping Artifacts and Challenges

The biggest challenge during mapping was keeping the map crisp and avoiding blurry or shifted walls. Fast or uneven motion can make the map less accurate because SLAM depends on noisy odometry and scan matching. To reduce this, we slowed down, avoided unnecessary turning, and revisited previously seen areas to improve consistency and loop closure.

### Saved Map

The completed map was saved as:
- `maps/map_eerc722.yaml`
- `maps/map_eerc722.pgm`

### Part 2 SLAM Evidence

#### Part 2 Completed SLAM Map
![Part 2 Completed SLAM Map](figures/goal2-rviz.png)

### Part 2 Navigation in the Real Room

After saving the map, we relaunched navigation using the saved map and used RViz2 to initialize the robot pose and send point-to-point navigation goals in the real room.

#### Part 2 Real-Room Navigation
![Part 2 Real-Room Navigation](figures/goal2-term.png)

---

## Part 3 — Jackal Simulation

The setup for the jackal simulation was similar to the turtlebot simulation. The clearpath libraries had to be installed by following their offboard computer tutorial. Additionally the simulation packages had to be installed as well. One main difference is that the nav2 stack publishes commands to the `/a300_0000/cmd_vel_nav'` instead of `/a300_0000/cmd_vel`. This issue can be solved by running the command below to map the topics to each other. The simulation did run significantly slower which is likely due to the more intesive Nav2 stack as well as there being more features to simulate for the jackal
```
ros2 run topic_tools relay /a300_0000/cmd_vel_nav /a300_0000/cmd_vel
```

#### Part 3 Map
![Part 3 Terminal Output](figures/jackal-map.png)

#### Part 3 Gazebo View
![Part 3 Gazebo View](figures/jackal-gazebo.png)

#### Part 3 RViz Navigation View
![Part 3 RViz Navigation View](figures/jackal.gif)


---

## AI Use Documentation

### How AI was used
- Used to help draft and structure the README

## Reid
My AI use was mainly for debugging purposes. I encountered some issuses with my python enviornment as well as the command velocity topics mentioned above. AI was used to help debug error messages and give potential fixes to explore. I lost some of my conversation with ChatGPT (due to account issues) but I have one of the prompts I asked it below. AI was only used during part 3.
> My clearpath simulation robot will not move when it gets a path. When I manually publish /cmd_vel it moves fine. I receive this issue from the nav2 stack periodically [controller_server-1] [WARN] [1774876678.009329846] [a300_0000.controller_server]: Control loop missed its desired rate of 20.0000 Hz. Current loop rate is inf Hz
---

## Conclusion

Through this project, we practiced simulated navigation, real-world SLAM mapping, and ROS 2-based robot control using the Nav2 stack. Part 1 demonstrated how localization, planning, and costmaps interact in simulation, while Part 2 showed the added complexity of real hardware, networking, teleoperation, and SLAM map quality. These exercises built a stronger practical understanding of autonomous navigation with ROS 2.
