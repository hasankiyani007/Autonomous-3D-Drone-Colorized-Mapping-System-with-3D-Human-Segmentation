# Autonomous 3D Drone Mapping with Semantic Understanding

A real-time drone-based SLAM system built on FAST-LIVO2, extended with AI-powered human detection for search and rescue applications.

## Overview

This project addresses a critical gap in disaster response: while SLAM systems excel at creating 3D maps of unknown environments, they lack semantic understanding of what's actually in those maps. By integrating 2D human segmentation directly into the 3D mapping pipeline, this system enables real-time detection of survivors in collapsed structures, hazardous terrain, and disaster zones.

The system processes LiDAR point clouds at 10 FPS alongside camera feeds for semantic segmentation, creating annotated 3D maps where human detections are spatially localized. This approach significantly reduces search time in rescue operations compared to traditional mapping-only systems.

## System Architecture

The pipeline consists of two sensor streams that both feed into FAST-LIVO2:

1. **LiDAR Stream**: Livox Avia point clouds are processed by FAST-LIVO2 for odometry and mapping
2. **Camera Stream**: FLIR Blackfly S images are processed through human segmentation, then fed into FAST-LIVO2 for semantic integration

```
┌──────────────┐         ┌──────────────────┐
│  Livox Avia  │────────▶│                  │
│   (LiDAR)    │         │                  │
└──────────────┘         │    FAST-LIVO2   │────────▶ 3D Map
                         │  (SLAM/Mapping) │
┌──────────────┐         │                  │         + Semantic
│ FLIR Blackfly│────────▶│  Human Detection │         Annotation
│   (Camera)   │         │    Integration   │
└──────────────┘         └──────────────────┘
```

This architecture ensures that raw LiDAR data flows into FAST-LIVO2 for accurate mapping while camera imagery is processed through human segmentation and then integrated into FAST-LIVO2 for semantic understanding. The result is an annotated 3D map with human location data.

## Hardware Setup

| Component | Model | Purpose |
|-----------|-------|---------|
| LiDAR | Livox Avia | 3D point cloud acquisition |
| Camera | FLIR Blackfly S | 2D imaging for semantic segmentation |
| Computing | Intel NUC 13 Pro | Onboard processing |
| Flight Controller | Pixhawk 6C | Flight control |
| IMU | Integrated in Livox Avia | Inertial odometry |

## Software Requirements

- Ubuntu 18.04~20.04
- ROS Noetic
- FAST-LIVO2 framework
- Custom FLIR camera driver
- PCL >= 1.8
- Eigen >= 3.3.4
- OpenCV >= 4.2
- Sophus (non-templated/double-only version)
- Vikit (camera models and math functions)

## Installation

### 1. Ubuntu and ROS

Install Ubuntu 18.04~20.04 and set up ROS Noetic.

### 2. Dependencies

```bash
# PCL >= 1.8
sudo apt install libpcl-dev

# Eigen >= 3.3.4
sudo apt install libeigen3-dev

# OpenCV >= 4.2
sudo apt install libopencv-dev
```

### 3. Sophus (Non-templated Version)

```bash
git clone https://github.com/strasdat/Sophus.git
cd Sophus
git checkout a621ff
mkdir build && cd build && cmake ..
make
sudo make install
```

### 4. Vikit

```bash
cd ~/catkin_ws/src
git clone https://github.com/xuankuzcr/rpg_vikit.git
```

### 5. FAST-LIVO2

```bash
cd ~/catkin_ws/src
git clone https://github.com/hku-mars/FAST-LIVO2
cd ../..
catkin_make
source ~/catkin_ws/devel/setup.bash
```

### 6. Custom FLIR Camera Driver

```bash
cd ~/catkin_ws/src
git clone https://github.com/hasankiyani007/flir_camera_driver_FASTLIVO2.git
cd ../..
catkin_make
source ~/catkin_ws/devel/setup.bash
```

### 7. Human Segmentation Node

Coming soon.

## Semantic 3D Human Segmentation

The human segmentation module subscribes to camera image topics and runs inference using a YOLO-based human detection model. Detected human locations are published as spatial annotations and fused into the FAST-LIVO2 point cloud map to produce semantic 3D output.

### Node Structure

```
┌─────────────────────┐    ┌─────────────────────┐    ┌─────────────────────┐
│  Image Subscriber   │───▶│  YOLO Detection    │───▶│  Semantic Fusion    │
│    (Camera)         │    │  Human Segmentation│    │   Point Cloud       │
└─────────────────────┘    └─────────────────────┘    └─────────────────────┘
                                                                  │
                                                                  ▼
                                                          Semantic 3D Map
```

## Performance

- **Mapping Frequency**: 10 FPS
- **Coverage**: 80-100 acres per 20-minute flight
- **Point Processing**: 200,000 points/second
- **End-to-end Latency**: ~50ms

## Related Repositories

- [FLIR Camera Driver (Custom Fork)](https://github.com/hasankiyani007/flir_camera_driver_FASTLIVO2) - ROS driver for FLIR Blackfly S integration
- [FAST-LIVO2](https://github.com/hku-mars/fast-livo2) - Original SLAM framework

## Acknowledgments

This project builds upon the excellent [FAST-LIVO2](https://github.com/hku-mars/fast-livo2) framework developed by the Hong Kong University Multi-Agent Robotics Lab. All credit for the SLAM subsystem goes to the original authors.

## License

MIT License - See individual component licenses for dependencies.

## Contact

- Email: hasankiyani001@gmail.com
- GitHub: github.com/hasankiyani007
- LinkedIn: linkedin.com/in/hasan-kiyani-490576294
