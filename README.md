# Autonomous Drone development for GPS-less Inspection
----
## 1. Introduction

Drones have been used for inspections which are done manually since a long time, but now increasingly they are being utilized for autonomous inspections. Most of the drones, currently in the market are dependent on GPS for navigating from one point to another. This dependency on GPS limits the application of the drones to outdoor inspections with reliable GPS. Expanding the abilities of drones to be utilized in non-gps environments can unlock new opportunities.

Intelligent imagery using drones is radically improving the inspection capabilities of humans. Drones are expanding the abilities of humans to capture large amounts of imagery at record speed and accuracy. However, the need for expertise to capture critical data using drones is hard to find and requires sufficient training. The autonomy of drones will enable a range of opportunities to remotely capture data and prevent unnecessary risks to the life of workers. During the flight, most drones require GPS, but not every place has a reliable GPS. Indoor environments and environments with high magnetic interference have either inaccurate GPS or no GPS connectivity. Thus, utilizing GPS for autonomy may limit the inspection capabilities of drones. 

----

## 2. Objective

Make drones autonomous and work without GPS in all environments. Features employed to bring autonomy are - **Simultaneous Localization and Mapping (SLAM), visualizer with the kinematic model**(drone), **UI to send goals**(drone), **patrolling**(waypoint traversal), and **inspection**(region of interest).

#### Applications: Inspection of buildings, construction sites, warehouses, solar plants, transmission lines, tunnels and mines, disaster sites and bridges.

----

## 3. Hardware + Software setup for drone

-   **Hardware setup**: Quadcopter platform with ArduPilot + VIO tracking camera (Intel Realsense T265) + companion computer.

-   **Software setup**: ROS and non-ROS, Realsense SDK (on companion computer) + ArduPilot firmware.

-   **Parameter configuration**: On companion computer and ArduPilot to send and receive VISION_POSE_ESTIMATE messages and camera pose transformation.

-   **Ground testing**: How to monitor and plot data messages on GCS to ensure that everything is running according to plan.

-   **Flight test**: Explore the operational envelope in different modes (Loiter, Guided, Auto ) and environment

-   **Troubleshooting guide**: Detail about solving the software and hardware setup issues.

----

## 4. Autonomous Navigation

To work autonomously and GPS-less, we used MAVROS packages - vision_to_mavros, realsense ROS (for Camera) and mavros transformations are used and detailed information is mentioned in simulation, vision_to_mavros and realsense repository.

----

## 5. Experiments and Flight test results

To run the simulation scripts autonomously, simulation repository has details about the experiments.
