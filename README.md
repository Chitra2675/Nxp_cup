# NXP India CUP 2026: Autonomous Medical Response

## <span style="background-color: #FFFF00">INTRODUCTION [TO UPDATE THE FILE]</span>
The NXP Cup 2026 – Autonomous Medical Response Challenge places participants in a realistic smart-city simulation where the NXP rover acts as an autonomous emergency response vehicle. Participants must develop a complete autonomous solution capable of navigating city roads, interpreting traffic guidance signs, identifying patients, communicating with municipal services, and delivering patients to their assigned hospitals.
The simulated city contains multiple intersections, buildings, road signs, obstacles, patient locations, hospitals, and decoy facilities. Using onboard sensors and perception algorithms, participants must autonomously travel through the city, locate patient buildings, scan QR codes, obtain hospital assignments from the Municipality Server, and safely transport patients to the correct destinations.
Throughout the challenge, teams will be required to integrate multiple robotics disciplines, including:

* Computer Vision
* Lane Following
* Sign Recognition
* QR Code Detection and Decoding
* Autonomous Navigation
* Mission Planning
* ROS2 Communication
* Obstacle Avoidance
* Decision Making

Successful scoring in the challenge depends on:
* reaching correct destinations 
* making correct navigation decisions
* communicating with external services
* avoiding incorrect deliveries
* and completing all assigned medical response missions within the allotted time.

The ultimate objective is to successfully transport all patients to their assigned hospitals, avoid decoy hospitals and obstacles, exit the city, and autonomously park the rover in the designated parking area, demonstrating a complete end-to-end autonomous medical response workflow. This is a Time-Bound Challenge.

- The `b3rb_ros_warehouse.py` script serves as a foundational ROS 2 node.
    * Participants will extend this script to implement the full challenge logic.

### <span style="background-color: #CBC3E3">HARDWARE</span>
This software is designed to run on the B3RB and can be tested in compatible Gazebo simulations.
1.  [NXP MR-B3RB](https://nxp.gitbook.io/mr-b3rb): The target hardware rover.
    * Requires a forward-facing camera for QR code detection and potentially building and sign recognition.
    * Relies on sensors (LIDAR, encoders, IMU) for localization & mapping, and navigation (Nav2).
2.  [Gazebo Simulator](https://gazebosim.org/home): For development and testing in a simulated city environment.
    * The simulation provides a B3RB model with sensors and necessary packages such as NAV2.

### <span style="background-color: #CBC3E3">SOFTWARE [UPDATE]</span>
This project is based on the autopilot project - [CogniPilot](https://cognipilot.org/) (AIRY Release for B3RB).
<br>
Refer the [CogniPilot AIRY Dev Guide](https://airy.cognipilot.org/) for information about it's various components.
<br>
- **ROS 2:** Targeted for Humble Hawksbill.
- **Navigation:** Relies on a fully functional Nav2 stack.
    * Configuration for Nav2 can be adjusted in `cranium/src/b3rb/b3rb_nav2/config`.
- **Object Recognition:** An external YOLO model is provided by default to publish the detected objects.
    * The objects are publish on `/shelf_objects` (`synapse_msgs/WarehouseShelf`) topic.
- **Python Libraries:**
    * `rclpy`: ROS 2 client library for Python.
    * `numpy`: For numerical operations, particularly with map data.
    * `opencv`: For image processing, crucial for QR code decoding.
    * `scipy`: For image analysis and spatial distance calculations.
    * `tkinter`: For the optional progress table GUI.
- **[Cranium](https://airy.cognipilot.org/cranium/about/)**: A ROS workspace that performs higher level computation for CogniPilot.
    * On the hardware B3RB, it runs on [NavQPlus](https://nxp.gitbook.io/navqplus/) IMX8MPLUS (Mission Computer).
    * On the Gazebo Simulator, it runs on the Ubuntu Linux machine.
    * Relevant packages (detailed later):
        1. **b3rb_ros_aim_india**
        2. **synapse_msgs**
        3. **dream_world**
        4. **b3rb**
    * Interaction with the `cerebri` via `/cerebri/out/status` and `/cerebri/in/joy` topics.
- This project includes a ROS2 Python package (b3rb_ros_aim_india) that integrates into Cranium.

---
## <span style="background-color: #FFFF00">Autonomous Medical Response CHALLENGE DESCRIPTION</span>

The primary objective is to maximize points by successfully completing emergency medical response missions across the city in the designated timeline.

### <span style="background-color: #CBC3E3; font-weight:bold">SIMULATION WORLD</span>
- **City**
    * The challenge takes place in a simulated smart city consisting of roads, intersections, buildings, sign boards, and obstacles.
    * Participants must autonomously navigate through the city, complete medical response missions, and safely reach assigned destinations.
- **Buildings**
    * The city contains 8 mission-critical buildings, each identified by a QR code.
    * Patient Buildings
        ```
        PATIENT_1
        PATIENT_2
        PATIENT_3
        ```
    * Hospital Buildings
        ```
        HOSPITAL_1
        HOSPITAL_2
        HOSPITAL_3
        ```
    * Fake Hospital Buildings
        ```
        FAKE_HOSPITAL_1
        FAKE_HOSPITAL_2
        ```
- **QR Codes**
    * Each building contains a QR code that uniquely identifies the building.
    * Participants must detect and decode these QR codes using the onboard camera.
- **Sign Recognition**
    * Road sign boards are placed throughout the city to guide navigation.
    * Each sign board provides directions to:
        * Patient Buildings: A, B, C
        * Hospital Buildings: X, Y, Z

    * Possible directions:
        ```
        ← Left
        ↑ Straight
        → Right
        ```
    Participants must interpret these sign boards and take the correct turns to reach their destination.
- **Municipality Server**
    After scanning a patient QR code, participants must send the patient ID to the Municipality Server.
    The server responds with the assigned hospital destination.
    * Example:
        ```
        PATIENT_1    
            ↓
        HOSPITAL_2
        ```

- **Mission Flow**
    ```
    Enter City
        ↓
    Find Patient
        ↓
    Scan QR
        ↓
    Contact Municipality Server
        ↓
    Receive Hospital Assignment
        ↓
    Reach Correct Hospital
        ↓
    Repeat for All Patients
        ↓
    Exit City
        ↓
    Park Vehicle
    ```
- **Obstacles**
    * The city contains obstacles placed on and around the roads.
    * Participants must avoid collisions while completing all missions within the allotted time.

### <span style="background-color: #CBC3E3; font-weight:bold">TASK WORKFLOW (PARTICIPANT IMPLEMENTATION)</span>

Participants are responsible for implementing the logic required to successfully complete the emergency response mission.

- **1. Enter the City**
    * Start from the designated launch area.
    * Enter the city road network.
    * Begin autonomous operation.

- **2. Locate a Patient Building**

    * Follow road lanes and sign boards.
    * Navigate through intersections.
    * Reach one of the patient buildings:
        ```
        PATIENT_1
        PATIENT_2
        PATIENT_3
        ```
- **3. Scan Patient QR Code**

    * Position the rover near the patient building.
    * Capture and decode the QR code.
    * Extract the patient identifier.

    Example:
        ```
        PATIENT_1
        ```

- **4. Contact the Municipality Server**

    * Publish the decoded patient ID to the Municipality Server.
    * Wait for the hospital assignment.

    Example:
    ```
    PATIENT_1
        ↓
    HOSPITAL_2
    ```
- **5. Navigate to the Assigned Hospital**

    * Follow the city sign boards.
    * Take the correct turns at intersections.
    * Reach the assigned hospital.
    
    Possible hospital assignments:
    ```
    HOSPITAL_1
    HOSPITAL_2
    HOSPITAL_3
    ```
- **6. Verify Hospital Delivery**

    * Scan the QR code at the destination building.
    * Verify that the scanned QR matches the hospital assigned by the Municipality Server.

    Example:
    ```
    Assigned : HOSPITAL_2
    Scanned  : HOSPITAL_2
    ```
    ✅ Patient Delivered
    
    Incorrect deliveries, including arrivals at:
    ```
    FAKE_HOSPITAL_1
    FAKE_HOSPITAL_2
    ```
    will not be considered successful.

- **7. Repeat for Remaining Patients**

    Continue the process until all patient missions have been completed.
    ```
    PATIENT_1
    PATIENT_2
    PATIENT_3
    ```

- **8. Exit the City**

    After all patient deliveries are completed, navigate towards the city exit.

- **9. Mission Completion**

    **Rule**: The challenge is considered complete immediately after the third patient is successfully delivered to the correct hospital.

- **Bonus Task – Exit and Parking**
    * **Task**: After successfully delivering the third patient, leave the city through the designated Exit Gate.
    * **Rule:** The Exit Gate is located **in front of** the final hospital delivery zone.
    * **Action:** Navigate through the Exit Gate and proceed to the parking area.
    * **Bonus:** Park the rover completely within the designated parking zone to earn bonus points.
    Successfully exiting the city and parking the rover may award additional bonus points.

### <span style="background-color: #CBC3E3; font-weight:bold">EVALUATION AND SCORING</span>

- **Evaluation**
    Participants will communicate with the evaluation framework through the designated ROS topics.
    The evaluation system will verify:
    * Correct patient QR code identification.
    * Successful communication with the Municipality Server.
    * Correct hospital assignment handling.
    * Successful delivery to the assigned hospital.
    * Avoidance of fake hospital deliveries.
    * Completion time.
    * Collision count.
    * Bonus exit and parking completion.

    ⚠️ Participants may interact with the Municipality Server multiple times during the mission. However, only successful patient-to-hospital delivery sequences will be considered for scoring.

- **Scoring**
    - **Patient Identification**
        +5 Points for each correctly decoded patient QR code.
        ```
        PATIENT_1
        PATIENT_2
        PATIENT_3
        ```
        Maximum: 15 Points

    - **Municipality Communication**
        +5 Points for each successful patient registration with the Municipality Server.
        
        Maximum: 15 Points

    - **Hospital Delivery**

        +10 Points for delivering a patient to the hospital assigned by the Municipality Server.

        Example:
        ```
        PATIENT_1
            ↓
        HOSPITAL_2
        ```
        Maximum: 30 Points

    - **Mission Completion Bonus**

        +20 Points for successfully completing deliveries for all three patients.
        ```
        PATIENT_1
        PATIENT_2
        PATIENT_3
        ```
        Maximum: 20 Points

    - **Time-Based Ranking**

        +15 Points awarded percentile-wise based on the total mission completion time.

        The timer stops immediately after the third patient is successfully delivered to the correct hospital.
        Maximum: 15 Points

    - **Collision Penalty**

        -1 Point for every collision with an obstacle or city infrastructure.


    - **Incorrect Delivery Penalty**

        -5 Points for delivering a patient to the wrong hospital.

        Example:
        ```
        Assigned : HOSPITAL_2
        Reached  : HOSPITAL_1
        ```

    - **Fake Hospital Penalty**

        -10 Points for attempting delivery at a fake hospital.
        ```
        FAKE_HOSPITAL_1
        FAKE_HOSPITAL_2
        ```

    - **Bonus Task Scoring**
        After the third patient delivery, participants may attempt the bonus task.
        * City Exit Bonus
            +5 Points for successfully exiting the city through the designated Exit Gate.
        * Parking Bonus
            +10 Points for autonomously parking the rover completely inside the marked parking zone.

        Maximum Bonus: 15 Points
    
    - **Maximum Score**
        ```
        Correct Patient Identification (3 × 5)       15
        Correct Hospital Deliveries (3 × 15)         45
        Mission Completion Bonus                     20
        Time-Based Ranking Bonus                     15
        Exit Bonus                                    5
        Parking Bonus                                10
        Attendance                                    5(based on no. of sessions attended)
        -----------------------------------------------
        Maximum Score                               115
        ```
        The winner will be the team with the highest final score. In the event of a tie, the team with the lower mission completion time will be ranked higher. 


---
## <span style="background-color: #FFFF00">`b3rb_ros_warehouse.py` SCRIPT FUNCTIONALITY OVERVIEW</span>

The `explore` node provides a foundational structure.

### <span style="background-color: #FFC0CB; font-weight:bold">SLAM MAP (INTRODUCTION & UNDERSTANDING)</span>
- The maps are created using LIDAR, IMU and odometry data.
- **Coordinate Frames:**
    1. **Occupancy Grid Frame:** SLAM maps are shared as [nav_msgs/OccupancyGrid](https://docs.ros.org/en/noetic/api/nav_msgs/html/msg/OccupancyGrid.html) which is a 2-D grid.
        * Each cell represents the probability (expressed as 0-100) of being occupied by an object.
        * Thus, 0 = free space, 100 = occupied by obstacle; -1 (special case) = unexplored space.
        * For conversion to world coordinate frame:
            * Origin: Position of the bottom-left corner of the map in the world frame in meters.
            * Resolution: Length of each grid cell in meters.
        * (0, 0) is the bottom-most and left-most cell in the map in the below diagrams.
    2. **World Coordinate Frame:** Real world coordinates in meters.
        * Origin: The starting point of the robot.
- **Types of Occupancy Grid Frames:**
    1. Static costmap (`/map`): Simple map used for quick decisions​
    2. Global costmap (`/global_costmap/costmap`): Static costmap + inflation
        * Inflation: The expanding of obstacles in the costmap to create a buffer zone around them.
        * It's used because the robot needs to account for its size and potential localization errors.
    * Axes: The axes for static and global maps are given as follows. (NOTE: x-axis is robot's front direction at starting.) <br>
    ![](resource/ros2_slam_axes.png)

### <span style="background-color: #FFC0CB; font-weight:bold">NAVIGATION FRAMEWORK (BASE FUNCTIONALITY)</span>
- **Core Navigation Logic:** The script offers a framework for navigation using a Nav2 action client.
    * Participants provide a goal pose, and the Nav2 stack manages robot movement.
    * **Goal:** Consists of x-y coordinates (in world coordinate frame) and yaw (angle about the z-axis).
        * NOTE: YAW is the angle (between 0 to 2π) from the Positive x-axis in CCW direction.
    * The Nav2 stack provides feedback on the current goal's status via a feedback callback.
    * A mechanism to cancel an ongoing navigation goal is included.
        * This can be used if a goal repeatedly fails or if a more optimal goal is found.
- **Autonomous Exploration Example (Frontier-Based):**
    * The script includes a demo frontier-based exploration approach for space exploration.
        * This demonstrates Nav2 usage and how the warehouse might be initially explored.
        * Participants have **full autonomy** to modify or replace this exploration logic.
    * **Frontier Detection (`get_frontiers_for_space_exploration`):**
        * Identify the boundaries between explored and unknown space.
        * Select the next exploration goal intelligently, considering proximity to obstacles.
- **Robot Arming:** Monitors `/cerebri/out/status` and attempts to arm via `/cerebri/in/joy`.

### <span style="background-color: #FFC0CB; font-weight:bold">WAREHOUSE INTERACTION (FRAMEWORK FOR CHALLENGE)</span>
- **Shelf Object Handling:**
    * Subscribes to `/shelf_objects` (`synapse_msgs/WarehouseShelf`): `self.shelf_objects_callback`.
        * **Participants must process `self.shelf_objects_curr` for task-specific object identification.**
    * Publishes to `/shelf_data` (`synapse_msgs/WarehouseShelf`): `self.publisher_shelf_data`.
        * **Participants must construct and send messages per challenge rules.**
- **QR Code Detection (Framework):**
    * Subscribes to `/camera/image_raw/compressed`: `self.camera_image_callback`.
        * **Participants must implement QR decoding logic here.**
    * Participants may store the the last decoded QR string in `self.qr_code_str`.
    * Optionally publish debug images for QR to `/debug_images/qr_code`.

### <span style="background-color: #FFC0CB; font-weight:bold">GUI - PROGRESS TABLE (OPTIONAL UTILITY)</span>
- **`WindowProgressTable` Class:** A Tkinter-based GUI.
- **Functionality:**
    * Displays a 2 x n grid, mapping to 2 rows and n shelves.
    * Can be enabled/disabled using the `PROGRESS_TABLE_GUI` flag.
    * This GUI is provided for participant convenience to track progress. It's use is entirely **optional**.
    * **Participants choosing to use it should integrate updates to reflect their challenge progress.**
- It's usage is described in `shelf_objects_callback`.

### <span style="background-color: #FFC0CB; font-weight:bold">KEY SUBSCRIBED TOPICS (RELEVANT TO CHALLENGE)</span>
- `/pose` ([geometry_msgs/PoseWithCovarianceStamped](https://docs.ros.org/en/noetic/api/geometry_msgs/html/msg/PoseWithCovarianceStamped.html)): Current robot pose.
- `/global_costmap/costmap` ([nav_msgs/OccupancyGrid](https://docs.ros.org/en/noetic/api/nav_msgs/html/msg/OccupancyGrid.html)): Global costmap from Nav2.
- `/map` ([nav_msgs/OccupancyGrid](https://docs.ros.org/en/noetic/api/nav_msgs/html/msg/OccupancyGrid.html)): Raw map from SLAM.
- `/cerebri/out/status` (`synapse_msgs/Status`): Robot's low-level controller status.
- `/shelf_objects` (`synapse_msgs/WarehouseShelf`): **YOLO model output.**
- `/camera/image_raw/compressed` ([sensor_msgs/CompressedImage](https://docs.ros.org/en/melodic/api/sensor_msgs/html/msg/CompressedImage.html)): **Image stream.**

### <span style="background-color: #FFC0CB; font-weight:bold">KEY PUBLISHED TOPICS (RELEVANT TO CHALLENGE)</span>
- `/cerebri/in/joy` ([sensor_msgs/Joy](https://docs.ros.org/en/noetic/api/sensor_msgs/html/msg/Joy.html)): For changing mode and arming the buggy.
- `/shelf_data` (`synapse_msgs/WarehouseShelf`): **Publish identified objects and QR data.**
- `/debug_images/qr_code` ([sensor_msgs/CompressedImage](https://docs.ros.org/en/melodic/api/sensor_msgs/html/msg/CompressedImage.html)): Optional for debugging QR processing.

### <span style="background-color: #FFC0CB; font-weight:bold">ACTION CLIENTS</span>
- **`MapsToPose` Action Client:** Used for sending navigation goals to Nav2 ([nav2_msgs/NavigateToPose](https://github.com/ros-planning/navigation2/blob/humble/nav2_msgs/action/NavigateToPose.action)).

---
## <span style="background-color: #FFFF00">`b3rb_ros_object_recog.py` SCRIPT FUNCTIONALITY OVERVIEW</span>

The `detect` node provides the framework for running the default quantized YOLOv5 object recog model. <br>
Participants may modify this or implement a different method for object recognition, for improving accuracy.

### <span style="background-color: #FFC0CB; font-weight:bold">KEY SUBSCRIBED TOPICS (Relevant to Challenge)</span>
- `/camera/image_raw/compressed` ([sensor_msgs/CompressedImage](https://docs.ros.org/en/melodic/api/sensor_msgs/html/msg/CompressedImage.html)): **Image stream.**

### <span style="background-color: #FFC0CB; font-weight:bold">KEY PUBLISHED TOPICS (Relevant to Challenge)</span>
- `/shelf_objects` (`synapse_msgs/WarehouseShelf`): **Publish identified objects.**
- `/debug_images/object_recog` ([sensor_msgs/CompressedImage](https://docs.ros.org/en/melodic/api/sensor_msgs/html/msg/CompressedImage.html)): Publish debug image.

---
## <span style="background-color: #FFFF00">`b3rb_ros_model_remove.py` SCRIPT FUNCTIONALITY OVERVIEW</span>

The `remover` node provides a script for removing curtains from shelves per challenge rules. <br>
Participants are not required to modify this script. This script will be used for evaluation as is.

---
## <span style="background-color: #FFFF00">`b3rb_ros_draw_map.py` SCRIPT FUNCTIONALITY OVERVIEW</span>

The `visualize` node helps visualizing the simple map created by  SLAM on a matplotlib window. <br>
Participants may utilize this to debug their solution. This script will not be run during evaluation.

---
## <span style="background-color: #FFFF00">EXECUTION STEPS</span>

**Requirements:**
1. [Ubuntu 22.04.5](https://releases.ubuntu.com/jammy/) (Fresh installation recommended to prevent any compatibility conflict with current setup)
2. Unrestricted internet (Official network such as college wifi is not recommended; use personal internet)

**General Guidelines**
1. Press `Y` and `enter` wherever necessary.
2. Enter **sudo password** wherever necessary.

Run the commands in code boxes (like the following) in the terminal window.
```
sudo apt install git
```

In case the installation or setup gets corrupted, run the following to clean the entire system: <br>
**(⚠️ ALERT: This is a nuclear option, will delete the whole setup and should be used carefully.)**
```
sudo apt-get remove gz-garden
sudo apt-get remove ros-humble-ros-gzgarden
sudo apt-get remove gz-harmonic
sudo apt-get remove ros-humble-ros-gzharmonic

sudo rm -rf /opt/toolchains
sudo rm -rf /opt/zeth
sudo rm -rf /opt/poetry
rm -rf ~/bin/build_*
rm -rf ~/bin/west
rm -rf ~/bin/cyecca
rm -rf ~/bin/docs
rm -rf ~/cognipilot
```

### <span style="background-color: #CBC3E3; font-weight:bold">PART 1</span>

**Install CogniPilot by executing the following steps (taken from [https://airy.cognipilot.org/getting_started/install/](https://airy.cognipilot.org/getting_started/install/)):**
1. NOTE:
    1. docker method is **not** recommended
    2. SSH and GPG keys are **not** required
2. Use CogniPilot universal installer: Open a terminal and run the following.
    ```
    sudo apt-get update
    sudo apt-get install git wget -y
    mkdir -p ~/cognipilot/installer
    wget -O ~/cognipilot/installer/install_cognipilot.sh https://raw.githubusercontent.com/CogniPilot/helmet/main/install/install_cognipilot.sh
    chmod a+x ~/cognipilot/installer/install_cognipilot.sh
    /bin/bash ~/cognipilot/installer/install_cognipilot.sh
    ```
    1. Select `1` (airy) when asked for 'release'
    2. Select `1` (native) when asked for 'installer type'
    3. If you are asked "Do you want to continue" then select `Y` and press enter
    4. Select `n` (No) when asked for 'ssh keys'
3. Build the workspace: Open a terminal and run the following.
    ```
    source ~/.profile
    source ~/.bashrc
    build_workspace
    ```
    1. select `n` (No) "for use ssh keys"
    2. select `1` (b3rb) "for platform"
4. Source ~/.bashrc.
    ```
    source ~/.bashrc
    ```
5. Build Foxglove.
    ```
    build_foxglove
    ```
    1. select `n` (No) when asked for 'ssh keys'
    2. select `1` (airy) when asked for 'release'

### <span style="background-color: #CBC3E3; font-weight:bold">PART 2[TO UPDATE]</span>

Open a terminal in a temp location and follow the following steps to setup **[NXP_AIM_INDIA_2025](https://github.com/NXPHoverGames/NXP_AIM_INDIA_2025)** and **YOLOv5**.
```
git clone https://github.com/NXPHoverGames/NXP_AIM_INDIA_2025 NXP_AIM_INDIA_2025
mv NXP_AIM_INDIA_2025 ~/cognipilot/cranium/src/

git clone https://github.com/NXPHoverGames/B3RB_YOLO_OBJECT_RECOG.git
cd B3RB_YOLO_OBJECT_RECOG
git checkout nxp_aim_india_2025

mv resource/coco.yaml ~/cognipilot/cranium/src/NXP_AIM_INDIA_2025/resource/
mv resource/yolov5n-int8.tflite ~/cognipilot/cranium/src/NXP_AIM_INDIA_2025/resource/
mv b3rb_ros_aim_india/b3rb_ros_object_recog.py ~/cognipilot/cranium/src/NXP_AIM_INDIA_2025/b3rb_ros_aim_india/

cd ..
rm -rf B3RB_YOLO_OBJECT_RECOG
```

 <span style="background-color:rgb(255, 238, 0); font-weight:bold">Then in "~/cognipilot/cranium/src/NXP_AIM_INDIA_2025/setup.py" uncomment lines 12, 13 and 28.</span>

Perform the following steps to setup the environment and build cranium for NXP_AIM_INDIA_2025:

1.  **Setup Environment:**
    ```bash
    cd ~/cognipilot/cranium/src/
    rm -rf dream_world
    rm -rf synapse_msgs
    rm -rf b3rb_simulator

    cd ~/cognipilot/cranium/src/
    git clone https://github.com/NXPHoverGames/dream_world.git
    cd ~/cognipilot/cranium/src/dream_world
    git checkout nxp_aim_india_2025

    cd ~/cognipilot/cranium/src/
    git clone https://github.com/NXPHoverGames/synapse_msgs.git
    cd ~/cognipilot/cranium/src/synapse_msgs
    git checkout nxp_aim_india_2025

    cd ~/cognipilot/cranium/src/
    git clone https://github.com/NXPHoverGames/b3rb_simulator.git
    cd ~/cognipilot/cranium/src/b3rb_simulator
    git checkout nxp_aim_india_2025
    ```

2.  **Install Dependencies:** (The following modules are allowed for use in your solution.)
    - **ALERT: If you wish to use an additional python module, refer "SUBMISSION RULES" below**
    ```bash
    pip install \
        torch==2.3.0 \
        torchvision==0.18.0 \
        numpy==1.26.4 \
        opencv-python==4.11.0.86 \
        scipy==1.15.1 \
        scikit-learn==1.5.2 \
        tk==0.1.0 \
        pyzbar==0.1.9 \
        matplotlib==3.5.1 \
        pyyaml==6.0.2 \
        tflite-runtime==2.14.0
    ```

### <span style="background-color: #CBC3E3; font-weight:bold">PART 3</span>

There are four simulation world environments which you can load for your testing.
* nxp_aim_india_2025/warehouse_1
* nxp_aim_india_2025/warehouse_2
* nxp_aim_india_2025/warehouse_3
* nxp_aim_india_2025/warehouse_4

The following world specific parameters are passed from the command line while running "ros2 launch":
* `warehouse_id`
* `initial_angle`
* `shelf_count`

Perform the following steps:

3.  **Build Workspace and Launch Gazebo Simulation:**

    NOTE: Whenever you make a change, `colcon build` and `source setup.bash` is required as follows. <br>
    ⚠️ NOTE: Execute only one of the "ros2 launch" commands, depending upon which world you wish to load.

    ```bash
    cd ~/cognipilot/cranium/
    colcon build
    source ~/cognipilot/cranium/install/setup.bash

    # execute one of the following depending on which world you wish to load.
    ros2 launch b3rb_gz_bringup sil.launch.py world:=nxp_aim_india_2025/warehouse_1 warehouse_id:=1 shelf_count:=2 initial_angle:=135.0 x:=0.0 y:=0.0 yaw:=0.0
    ros2 launch b3rb_gz_bringup sil.launch.py world:=nxp_aim_india_2025/warehouse_2 warehouse_id:=2 shelf_count:=4 initial_angle:=040.6 x:=0.0 y:=-7.0 yaw:=1.57
    ros2 launch b3rb_gz_bringup sil.launch.py world:=nxp_aim_india_2025/warehouse_3 warehouse_id:=3 shelf_count:=3 initial_angle:=045.0 x:=5.0 y:=-2.0 yaw:=3.14
    ros2 launch b3rb_gz_bringup sil.launch.py world:=nxp_aim_india_2025/warehouse_4 warehouse_id:=4 shelf_count:=5 initial_angle:=045.0 x:=5.0 y:=-2.0 yaw:=3.14
    ```

    <span style="background-color:rgb(255, 238, 0); font-weight:bold">This will open two new windows - "cerebri terminal" (never use this shell) and "Gazebo Sim".</span>

    ⚠️ NOTE: If you get a simulation launch create error (like the following), then cancel the above command & run `ros2 launch` again.
    ![](resource/sim_create_error.png)


### <span style="background-color: #CBC3E3; font-weight:bold">PART 4</span>

(OPTIONAL: For visualizing slam map.)

4.  **Open a new terminal and follow the following steps for running b3rb_ros_draw_map:**
    ```bash
    source ~/cognipilot/cranium/install/setup.bash
    ros2 run b3rb_ros_aim_india visualize
    ```

(OPTIONAL: For debugging with Foxglove.)

5. Clone and checkout `electrode` from NXPHoverGames using the following steps:
    ```
    cd ~/cognipilot/electrode/src/
    rm -rf electrode
    git clone https://github.com/NXPHoverGames/electrode.git
    cd electrode
    git checkout nxp_aim_india_2025
    ```
    This commit appends the following in the **default_value** of **topic_whitelist** in **[electrode.launch.py](https://github.com/CogniPilot/electrode/blob/airy/launch/electrode.launch.py)**:
    1. "/debug_images/object_recog"
    2. "/debug_images/qr_code"

6. **Open a new terminal and follow the following steps for building and running Foxglove.**
    ```bash
    cd ~/cognipilot/electrode/
    colcon build
    source ~/cognipilot/electrode/install/setup.bash
    ros2 launch electrode electrode.launch.py sim:=True
    ```

    - Sign in with your personal account (you may need to create an account on Foxglove); <br>
    - Then connect to simulation by clicking on "Open connection", then set WebSocket URL as "ws://localhost:8765". <br>
    - Click on 'layout', click on 'import from file', select "~/cognipilot/electrode/src/electrode/foxglove_layouts/b3rb.json". <br>
    ![](resource/foxglove_layout.png)

    ⚠️ NOTE: If `/cerebri/out/status` are in **Waiting** state (like in the following), then cancel all commands and restart from step 3.
    ![](resource/waiting_state_error.png)

## <span style="background-color: #FFFF00">ADVANCED STEPS FOR FASTER DEVELOPMENT (OPTIONAL)</span>

You may create a bash file (unique for each world) for faster execution. For example for warehouse_1:
```
cd ~/cognipilot/cranium/
colcon build
source install/setup.bash
ros2 launch b3rb_gz_bringup sil.launch.py world:=nxp_aim_india_2025/warehouse_1 warehouse_id:=1 shelf_count:=2 initial_angle:=135.0 x:=0.0 y:=0.0 yaw:=0.0
```

## <span style="background-color: #FFFF00">`cranium/src/b3rb/b3rb_nav2` PACKAGE FUNCTIONALITY OVERVIEW</span>
Participants are allowed to modify the following files in the NAV2 package in CRANIUM to enhance navigation. <br>
Participants are encouraged to tune the configuration parameters based on their algorithm & performance needs. <br>

1. **~/cognipilot/cranium/src/b3rb/b3rb_nav2/config/nav_to_pose_bt.xml**
    - It defines the behavior tree for navigating to a single goal pose.
2. **~/cognipilot/cranium/src/b3rb/b3rb_nav2/config/nav_through_poses_bt.xml**
    - It defines the behavior tree for navigating through multiple waypoints.
3. **~/cognipilot/cranium/src/b3rb/b3rb_nav2/config/nav2.yaml**
    - Central configuration for the Nav2 (ROS2 navigation) stack.
    - Key parameters: obstacle inflation, goal tolerances, recovery behaviors, global and local parameters.
4. **~/cognipilot/cranium/src/b3rb/b3rb_nav2/config/slam.yaml**
    - It defines the configuration parameters for slam_toolbox (Simultaneous Localization and Mapping).
    - Key parameters: map resolution, update intervals, scan throttling, loop closure settings.

⚠️ NOTE: Modifying parameters involves tradeoff between the following:
- Mapping accuracy
- CPU usage
- Responsiveness
- Robustness.
- Stability

## <span style="background-color: #FFFF00">PARTICIPANT IMPLEMENTATION FOCUS</span>
Participants should modify and extend `b3rb_ros_warehouse.py` and `b3rb_ros_object_recog.py` to:
-   **Shelf Detection:** Implement robust logic for finding shelf locations (map-based or vision-based).
    - map based: `global_map_callback` or `simple_map_callback`
    - image based (taken from front of the robot): `camera_image_callback`
-   **Targeted Navigation:** Develop functions for precise navigation to:
    * The front/back of shelves for object viewing.
    * QR code locations on either side of shelves.
-   **Navigation Recovery:** The Nav2 action client may collide the robot with obstacles if not tuned correctly.
    * ⚠️ **Participants are strongly advised to implement a recovery logic** by cancelling current goal.
    * Then, move robot away from the obstacle using manual mode after analyzing the map.
-   **State Management:** Track the status of n shelves (IDs, objects, QR codes, curtain revealed or not).
-   **Object Data Processing:** In `shelf_objects_callback`, associate identified objects with shelves.
-   **QR Code Decoding:** Implement reliable QR code detection/decoding in `camera_image_callback`.
-   **Challenge Strategy:** Design the overall approach:
    * Explore world using heuristic.
    * Visiting shelves in sequence.
    * Error handling and recovery.
-   **Data Publication Logic:** Create message and publish to `/shelf_data` per scoring rules.
-   **GUI Integration (Optional):** If using `WindowProgressTable`, update it to reflect challenge state.
-   NOTE: As a fallback, participants may discard the heuristic and explore randomly to locate shelves.
    * Then, visit all shelves in order and perform the necessary tasks.

## <span style="background-color: #FFFF00">AREAS FOR DEVELOPMENT IN THE WAREHOUSE SCRIPT</span>
- **Shelf Detection:** The base script lacks specific shelf detection logic; this is a key participant task.
- **Navigating to Shelves**: Position the robot such that shelf objects are clearly visible by front camera.
- **QR Code Decoding:** `camera_image_callback` is a placeholder requiring implementation.
- **Comprehensive State Machine:** A robust state machine for challenge workflow management.
- **Object Recognition:** Improve the default object recognition module provided, if needed.
    * Areas of improvement for object recognition: Accuracy, inference time, output classes.

## <span style="background-color: #FFFF00">SUBMISSION RULES</span>
⚠️ **NOTE: NXP laptop will be used for evaluation. No additional package installation will be allowed.** <br>
**The code should work with the default setup created at the time of installing CogniPilot Airy release.** <br> <br>
**NOTE: Additional python modules may be permitted only after written consent from the AIM TEAM.**
- Contact NXP AIM Technical Team if you wish to use a python module not in the following list:
    - torch==2.3.0, torchvision==0.18.0, numpy==1.26.4, opencv-python==4.11.0.86, scipy==1.15.1,
    - scikit-learn==1.5.2, tk==0.1.0, pyzbar==0.1.9, matplotlib==3.5.1, pyyaml==6.0.2, tflite-runtime==2.14.0.

**Participants will submit the following for the final evaluation:**
1. b3rb_ros_aim_india
    - NOTE: Unset PROGRESS_TABLE_GUI at b3rb_ros_warehouse.py:56
2. b3rb: The following files only.
    - ~/cognipilot/cranium/src/b3rb/b3rb_nav2/config/nav_to_pose_bt.xml
    - ~/cognipilot/cranium/src/b3rb/b3rb_nav2/config/nav_through_poses_bt.xml
    - ~/cognipilot/cranium/src/b3rb/b3rb_nav2/config/nav2.yaml
    - ~/cognipilot/cranium/src/b3rb/b3rb_nav2/config/slam.yaml