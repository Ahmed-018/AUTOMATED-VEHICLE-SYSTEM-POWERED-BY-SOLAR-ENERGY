# Automated Vehicle System Powered by Solar Energy

**Senior Design Project (EE499), King Abdulaziz University, Grade A+**
Team 38: Abdullah Binsalman, Mohammed Al-Baiti, Ahmed Al-Harthy
Advisor: Dr. Sultan Alghamdi | Sep 2025 to May 2026

A low-speed electric vehicle for the KAU campus that drives itself to a destination chosen on a map, stops automatically when a person steps in front of it, and charges from a solar panel.

🎥 **Project page and demo video:** [ahmed-018.github.io/solar-vehicle.html](https://ahmed-018.github.io/solar-vehicle.html)
📄 **Full report:** [Senior Design Report (PDF)](https://ahmed-018.github.io/assets/docs/Solar-Vehicle-Senior-Design-Report.pdf)

![The prototype](https://ahmed-018.github.io/assets/img/vehicle-hero.jpg)

---

## How it works

The vehicle runs **ROS2 Humble** on a laptop. The laptop reads the **2D LiDAR** and a **wide-angle camera**, plans a path with **Nav2**, and sends speed and steering commands over serial to an **Arduino Uno**. The Arduino then drives the steering servo and the DC motors (through a BTS7960 motor driver).

![Software architecture](https://ahmed-018.github.io/assets/img/ros2-architecture.jpg)

| Subsystem | Main parts |
|---|---|
| **Navigation** | `map_server`, AMCL localization, Nav2 / `bt_navigator`, RF2O laser odometry |
| **User interface** | Website or RViz goal → `rosbridge` → Nav2 |
| **Movement** | `ArduinoBridge.py` turns `/cmd_vel` into serial commands → Arduino → servo + motors |
| **Safety stop** | `camera_processor` (YOLO human detection) publishes `/camera_stop_signal` to stop the vehicle |

## Simulation and navigation

Before driving the real vehicle, the full ROS2 stack was tested in **Gazebo**: a virtual model of the car with a simulated LiDAR (blue rays) driving through a test track with obstacles.

![Gazebo simulation](https://ahmed-018.github.io/assets/img/gazebo-sim.gif)

On the saved map, **Nav2** plans a path (green line) to the selected goal and drives the vehicle there, while **AMCL** tracks its position. The shaded area around the vehicle is the local costmap built from the LiDAR.

![Nav2 navigation in RViz2](https://ahmed-018.github.io/assets/img/rviz-nav2.gif)

🎥 Higher-quality videos: [project page](https://ahmed-018.github.io/solar-vehicle.html)

## Hardware

- Laptop (Intel Core i7) running ROS2 Humble
- RPLIDAR A1 (2D LiDAR) and USB wide-angle camera
- Arduino Uno with an optical flow sensor
- BTS7960 motor driver, EV motors, 25 kg steering servo
- 100 W solar panel, MPPT charge controller, 12 V LiFePO4 battery

![Hardware block diagram](https://ahmed-018.github.io/assets/img/hardware-diagram.jpg)

## Results

| Test | Requirement | Measured | Status |
|---|---|---|---|
| Destination accuracy | Stop within 1 m of the goal | 5 / 5 trials, 0.11 to 0.72 m error | ✅ Pass |
| Human detection | Detect every time, react in < 0.25 s | 5 / 5 detected, up to 11.7 m | ✅ Pass |
| Speed | 0 to 25 km/h | ~2.4 km/h, stable | ✅ Pass |
| Stopping distance | Short, safe stop | 0.13 to 0.18 m | ✅ Pass |
| Solar charging efficiency | 75 to 85% | 75.2 to 83.7% | ✅ Pass |

## Team roles

| Member | Role |
|---|---|
| Abdullah Binsalman | System design and integration |
| Mohammed Al-Baiti | Hardware and circuit design (incl. solar charging station) |
| **Ahmed Al-Harthy** | Embedded systems and software: ROS2 nodes, Nav2 navigation, YOLO human detection |

## Running it

1. Install **ROS2 Humble** on Ubuntu 22.04, plus Nav2 and the RPLIDAR driver.
2. Clone this repo into your ROS2 workspace's `src` folder and build it with `colcon build`.
3. Upload the Arduino sketch to the Arduino Uno.
4. Launch the robot (LiDAR, camera, Arduino bridge, Nav2), then choose a destination in RViz or on the website.

> Exact package and launch file names are in the `src` folder of this repository.

## Future work

- Replace the laptop with an embedded computer (e.g. Jetson Orin Nano)
- 3D LiDAR, IMU and depth camera for outdoor navigation
- Better obstacle classification with a trained deep learning model
