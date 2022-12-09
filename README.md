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
    
    <img src="https://github.com/sagarbpatel31/drone_navigation/blob/main/simulation/Images/drone_frame.jpg" title="Drone Frame" width="30%" height="30%">
    <img src="https://github.com/sagarbpatel31/drone_navigation/blob/main/simulation/Images/drone.jpg" title="Drone" width="30%" height="30%">
    <img src="https://github.com/sagarbpatel31/drone_navigation/blob/main/simulation/Images/flying_drone.jpg" title="Test Drone" width="30%" height="30%">
   

-   **Software setup**: ROS and non-ROS, Realsense SDK (on companion computer) + ArduPilot firmware.

    GCS (Ground Control Station) Used – Mission Planner & QGroundControl 
    After Hardware Setup, Load Firmware in MRO (FCU) using GCS Software
    Selection of Frame – ‘X’ type Quad Frame
    Calibrations – Radio, Accelerometer, Compass, Level Horizon, ESC, Motor Tests
    
    Ground Control Station Software

    ### Mission Planner
    Using Mission Planner for following functionalities for Drone setup and flying manually – Connecting using USB connector.
    - Setup of Drone – Selection of Frame & Installing firmware in mRo (Flight Controller)
    - Calibrations of Accelerometer, Compass, ESC and Motor Setup with hardware. In Calibration of compass, it shows the offset and which compass we select for calibration.
    - Calibration of Radio TX-RX (Taranis QX7 transmitter remote)
    - Motor test with different Throttle speed.
    - Checking the detailed information on data page (Compass data, errors if any regarding the hardware failsafe, compass or others) 
    - Manually Arming, disarming the drone & checking the altitude, speed and other parameters in the visualization page.
    - Downloading the log and analyzing from DataFlash Logs
    - Configuring the parameters based on the applications (Manual – GPS or autonomous – non-GPS).
    
    For Autonomous Control, we are connecting the Mission Planner using UDP port with specific baud rate wirelessly and allocating the home position of drone to setup for navigation.
    After connecting successfully, it can ARM/DisARM from the actions module, or else it will give errors in compass page in top left section in data page.

    ### QGroundControl
    
    Detailed Ground Control Station Software with calibrations and setups in effective manner. Checks for the continuous setup of drone from selecting the frame to fly the drone considering all the calibrations and setup needed.
    
    During compass calibration, we can select the compass (internal or external) with the offset for calibration. The result shows the success ratio of calibration with 3 color ratio.
    
    Also, it has detailed calibrations of accelerometer, gyroscope, levelling, motor ESC and Radio calibration.

-   **Parameter configuration**: On companion computer and ArduPilot to send and receive VISION_POSE_ESTIMATE messages and camera pose transformation.

-   **Ground testing**: How to monitor and plot data messages on GCS to ensure that everything is running according to plan.

-   **Flight test**: Explore the operational envelope in different modes (Loiter, Guided, Auto ) and environment

----

## 4. Autonomous Navigation

To work autonomously and GPS-less, we used MAVROS packages - vision_to_mavros, realsense ROS (for Camera) and mavros transformations are used and detailed information is mentioned in simulation, vision_to_mavros and realsense repository.

### Mapping

We used gmapping to record the maps. The Gmapping package contains a ROS node which is called slam_gmapping, this node allows us to create a 2D map. SLAM mapping node allows us to create a 2D map by using the data from the laser and from the position of the drone. This node basically reads data from the laser and transforms the drone and turns this data into an OGM an occupancy grid map. Occupancy is represented as an integer in the range (0,100). With zero meaning completely free and 100 meaning completely occupied and a special value of -1 for completely unknown.


### Locaization

When the drone moves around a map, it needs to know what its position is within the map and what is its orientation i.e., location and rotation (pose of the drone).

For Localization we use ROS package called AMCL. It provides the amcl node, which uses the MCL system in order to track the localization of the drone moving in a 2D space. This node subscribes to the data of the laser, laser-based map and the transformations of the drone, and publishes its estimated position in the map.

### Global Planner

When a new goal is received, the goal is sent to the global planner. Then, the global planner is in charge of calculating the safe path in order to arrive at the goal pose. This path is calculated before the drone starts moving, so it will not consider the readings that the drone sensors are doing while moving like odometry, laser data etc. General dijkstra’s algorithm is implemented for this task. But this planner does not only use the map generated while mapping the surroundings, it uses another map called costmap. The global planner uses global costmap in order to calculate the path to follow. 

### Obstacle Avoidance:

**Local Planner**

Once the global planner has created a map to follow, the path is sent to the local planner. The local planner, then will execute each segment of the global plan. So, given a plan to follow and a map the local planner will provide velocity commands in order to move the drone. 
The local planner monitors the odometry and the laser data and chooses a collision-free local plan for the drone. So, the local planner will recompute the drones path on the fly in order to keep the drone from striking the objects, yet allowing it to reach its destination. 

This is implemented using the Trajectory Rollout and Dynamic Window Approach (DWA) algorithms. The basic idea of how the algorithm works is as follows:
- Discretely sample from the drones control space
- For each sampled velocity, perform forward simulations from the drone’s current state to predict what would happen if the sampled velocity is applied
- Evaluate each trajectory resulting from the forward simulation
- Discard illegal trajectories
- Pick the highest-scoring trajectory and send the associated velocities to the mobile base
- Rinse and Repeat

DWA differs from Trajectory Rollout in how the drone’s space is sampled. Trajectory Rollout samples are from the set of achievable velocities over the entire forward simulation period given the acceleration limits of the drone, while DWA samples are from the set of achievable velocities for just one simulation step given the acceleration limits of the drone. DWA is more efficient algorithm because it samples a smaller space, but maybe outperformed by Trajectory Rollout for drones with low acceleration limits because DWA does not forward simulate constant accelerations.

The local planner uses local costmap in order to calculate local plans and if the drone is struck somewhere the clear costmap command will be called.

**Inspection**

An inspection behavior is created with the help of navigation system of the drone by providing a sequence of points that the drone has to visit in order to cover the required area. Location and pose of the waypoints are obtained by individually giving the goal to the drone and reading that data from rostopic info of the goal. An action client to the move_base action server is created and desired goals are given stored in a variable.

----

## 5. Experiments and Flight test results

To run the simulation scripts autonomously, simulation repository has details about the experiments.
