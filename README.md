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

    1. Drone Frame - Tarrot 650
    2. Flight controller - mRo x2.1
    3. Companion Computer - Jetson Nano
    4. Stereo Camera - Intel RealSense T265
    5. LIDAR - RP Lidar A1
    6. GPS Module - mRo u-Blox Neo-M9N
    7. RF Receiver & Transmitter
    8. ESCs, Motors and Propellers
    9. 6 cell LiPo Battery (22.4V)
    
    ![Tarot Frame](https://github.com/sagarbpatel31/drone_navigation/blob/main/simulation/Images/drone_frame.jpg)
    ![Drone](https://github.com/sagarbpatel31/drone_navigation/blob/main/simulation/Images/drone.jpg)
    ![Drone Test](https://github.com/sagarbpatel31/drone_navigation/blob/main/simulation/Images/flying_drone.jpg)
   

-   **Software setup**: ROS and non-ROS, Realsense SDK (on companion computer) + ArduPilot firmware.

    GCS (Ground Control Station) Used – Mission Planner & QGroundControl 
    After Hardware Setup, Load Firmware in MRO (FCU) using GCS Software
    Selection of Frame – ‘X’ type Quad Frame
    Calibrations – Radio, Accelerometer, Compass, Level Horizon, ESC, Motor Tests
    
    Ground Control Station Software

    Mission Planner
    Using Mission Planner for following functionalities for Drone setup and flying manually – Connecting using USB connector.
    (a)	Setup of Drone – Selection of Frame & Installing firmware in mRo (Flight Controller)
    (b)	Calibrations of Accelerometer, Compass, ESC and Motor Setup with hardware. In Calibration of compass, it shows the offset and which compass we select for calibration.
    (c)	Calibration of Radio TX-RX (Taranis QX7 transmitter remote)
    (d)	Motor test with different Throttle speed.
    (e)	Checking the detailed information on data page (Compass data, errors if any regarding the hardware failsafe, compass or others) 
    (f)	Manually Arming, disarming the drone & checking the altitude, speed and other parameters in the visualization page.
    (g)	Downloading the log and analyzing from DataFlash Logs
    (h)	Configuring the parameters based on the applications (Manual – GPS or autonomous – non-GPS).
    For Autonomous Control, we are connecting the Mission Planner using UDP port with specific baud rate wirelessly and allocating the home position of drone to setup for navigation.
    After connecting successfully, it can ARM/DisARM from the actions module, or else it will give errors in compass page in top left section in data page.

    QGroundControl
    Detailed Ground Control Station Software with calibrations and setups in effective manner. Checks for the continuous setup of drone from selecting the frame to fly the drone considering all the calibrations and setup needed.
    During compass calibration, we can select the compass (internal or external) with the offset for calibration. The result shows the success ratio of calibration with 3 color ratio.
    Also, it has detailed calibrations of accelerometer, gyroscope, levelling, motor ESC and Radio calibration.



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
