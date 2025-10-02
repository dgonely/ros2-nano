# ROS2 Nano - Essential Concepts Guide

A comprehensive yet concise guide to ROS2 (Robot Operating System 2) fundamental concepts organized by categories for quick reference and learning.

## Table of Contents
- [Core Architecture](#core-architecture)
- [Communication Patterns](#communication-patterns)
- [Node Management](#node-management)
- [Data Types & Interfaces](#data-types--interfaces)
- [Package Structure](#package-structure)
- [Build System](#build-system)
- [Quality of Service (QoS)](#quality-of-service-qos)
- [Launch System](#launch-system)
- [Command Line Tools](#command-line-tools)
- [Lifecycle Management](#lifecycle-management)
- [Security](#security)
- [Cross-Platform Support](#cross-platform-support)

---

## Core Architecture

### **Distributed System**
- **DDS (Data Distribution Service)**: Middleware providing real-time, peer-to-peer communication
- **Discovery**: Automatic node and topic discovery without central broker
- **Decentralized**: No single point of failure, nodes communicate directly
- **Real-time**: Deterministic communication with timing guarantees

### **Computation Graph**
- **Nodes**: Independent processes that perform computation
- **Topics**: Named buses for asynchronous message passing
- **Services**: Synchronous request-response communication
- **Actions**: Long-running tasks with feedback and preemption

---

## Communication Patterns

### **Publishers & Subscribers**
```bash
# Publisher example
ros2 topic pub /chatter std_msgs/msg/String "data: 'Hello ROS2'"

# Subscriber example
ros2 topic echo /chatter
```

### **Service Clients & Servers**
```bash
# Call a service
ros2 service call /add_two_ints example_interfaces/srv/AddTwoInts "{a: 1, b: 2}"

# List services
ros2 service list
```

### **Action Clients & Servers**
```bash
# Send action goal
ros2 action send_goal /fibonacci action_tutorials_interfaces/action/Fibonacci "{order: 5}"

# List actions
ros2 action list
```

---

## Node Management

### **Node Lifecycle**
- **Unconfigured**: Initial state after creation
- **Inactive**: Configured but not processing data
- **Active**: Fully operational and processing
- **Finalized**: Cleaned up and ready for destruction

### **Node Information**
```bash
# List running nodes
ros2 node list

# Node information
ros2 node info /node_name

# Run a node
ros2 run package_name executable_name
```

---

## Data Types & Interfaces

### **Message Types**
- **Primitive Types**: `bool`, `int8/16/32/64`, `uint8/16/32/64`, `float32/64`, `string`
- **Arrays**: Fixed (`int32[5]`) and dynamic (`int32[]`) arrays
- **Nested Messages**: Complex structures with multiple fields

### **Common Standard Messages**
```bash
# Geometry messages
geometry_msgs/msg/Twist          # Linear and angular velocity
geometry_msgs/msg/PoseStamped    # Position and orientation with timestamp

# Sensor messages
sensor_msgs/msg/Image            # Camera images
sensor_msgs/msg/LaserScan        # Lidar data
sensor_msgs/msg/PointCloud2      # 3D point clouds

# Standard messages
std_msgs/msg/String              # Simple text
std_msgs/msg/Header              # Timestamp and frame info
```

### **Custom Interfaces**
```bash
# Generate interfaces
ros2 interface show package_name/msg/MessageName
ros2 interface show package_name/srv/ServiceName
ros2 interface show package_name/action/ActionName
```

---

## Package Structure

### **Package Layout**
```
my_package/
├── package.xml                  # Package metadata
├── CMakeLists.txt              # Build configuration (C++)
├── setup.py                    # Build configuration (Python)
├── setup.cfg                   # Python package configuration
├── resource/                   # Package marker files
├── src/                        # Source code (C++)
├── my_package/                 # Python modules
├── include/my_package/         # Headers (C++)
├── launch/                     # Launch files
├── config/                     # Configuration files
├── msg/                        # Message definitions
├── srv/                        # Service definitions
└── action/                     # Action definitions
```

### **Package Commands**
```bash
# Create package (Python)
ros2 pkg create my_package --build-type ament_python

# Create package (C++)
ros2 pkg create my_package --build-type ament_cmake

# List packages
ros2 pkg list
```

---

## Build System

### **Colcon (COLor CONstruction)**
```bash
# Build workspace
colcon build

# Build specific packages
colcon build --packages-select my_package

# Build with debugging
colcon build --cmake-args -DCMAKE_BUILD_TYPE=Debug

# Source workspace
source install/setup.bash
```

### **Workspace Structure**
```
ros2_workspace/
├── src/                        # Source packages
├── build/                      # Build artifacts
├── install/                    # Installation files
└── log/                        # Build logs
```

---

## Quality of Service (QoS)

### **QoS Profiles**
- **Reliability**: `RELIABLE` vs `BEST_EFFORT`
- **Durability**: `TRANSIENT_LOCAL` vs `VOLATILE`
- **History**: `KEEP_LAST` vs `KEEP_ALL`
- **Deadline**: Maximum time between messages
- **Lifespan**: Maximum age of messages

### **Predefined Profiles**
```python
# Common QoS profiles
qos_profile_sensor_data         # Best effort, volatile
qos_profile_default            # Reliable, volatile
qos_profile_system_default     # Reliable, transient local
qos_profile_services_default   # Reliable, volatile
```

---

## Launch System

### **Launch Files**
```python
# Python launch file example
from launch import LaunchDescription
from launch_ros.actions import Node

def generate_launch_description():
    return LaunchDescription([
        Node(
            package='my_package',
            executable='my_node',
            name='my_node_name',
            parameters=[{'param_name': 'param_value'}]
        )
    ])
```

### **Launch Commands**
```bash
# Run launch file
ros2 launch my_package my_launch_file.launch.py

# Launch with arguments
ros2 launch my_package my_launch_file.launch.py arg_name:=arg_value
```

---

## Command Line Tools

### **Essential Commands**
```bash
# Topic operations
ros2 topic list                 # List all topics
ros2 topic echo /topic_name     # Monitor topic messages
ros2 topic hz /topic_name       # Check publishing frequency
ros2 topic bw /topic_name       # Check bandwidth usage

# Parameter operations
ros2 param list                 # List all parameters
ros2 param get /node_name param_name
ros2 param set /node_name param_name value

# Bag operations (data recording)
ros2 bag record /topic1 /topic2
ros2 bag play my_bag
ros2 bag info my_bag

# System information
ros2 doctor                     # System health check
ros2 wtf                        # What's the failure (debugging)
```

---

## Lifecycle Management

### **Managed Nodes**
```cpp
// C++ lifecycle node example
class MyLifecycleNode : public rclcpp_lifecycle::LifecycleNode
{
public:
  CallbackReturn on_configure(const rclcpp_lifecycle::State &) override;
  CallbackReturn on_activate(const rclcpp_lifecycle::State &) override;
  CallbackReturn on_deactivate(const rclcpp_lifecycle::State &) override;
  CallbackReturn on_cleanup(const rclcpp_lifecycle::State &) override;
};
```

### **Lifecycle Commands**
```bash
# Control lifecycle nodes
ros2 lifecycle set /node_name configure
ros2 lifecycle set /node_name activate
ros2 lifecycle get /node_name
```

---

## Security

### **Security Features**
- **SROS2**: Secure ROS2 with authentication and encryption
- **Access Control**: Fine-grained permissions for topics and services
- **Certificate Management**: PKI-based security infrastructure
- **Network Encryption**: DDS security plugins for encrypted communication

### **Security Commands**
```bash
# Generate security artifacts
ros2 security generate_artifacts -k keystore -p policies
```

---

## Cross-Platform Support

### **Supported Platforms**
- **Linux**: Ubuntu (primary), Debian, RHEL, Arch
- **Windows**: Windows 10/11 with Visual Studio
- **macOS**: Limited support, community-driven
- **Real-time**: RT-kernel support for deterministic behavior

### **Middleware Options**
- **Fast DDS**: Default, high-performance implementation
- **Cyclone DDS**: Eclipse Foundation, focus on interoperability
- **Connext DDS**: RTI commercial implementation

---

## Getting Started

### **Installation**
```bash
# Ubuntu/Debian (apt)
sudo apt update
sudo apt install ros-humble-desktop

# Source setup
source /opt/ros/humble/setup.bash
```

### **First Steps**
1. **Create workspace**: `mkdir -p ~/ros2_ws/src`
2. **Create package**: `ros2 pkg create my_first_package`
3. **Build**: `colcon build`
4. **Source**: `source install/setup.bash`
5. **Run**: `ros2 run my_first_package my_node`

### **Learning Resources**
- **Official Tutorials**: [docs.ros.org](https://docs.ros.org/en/humble/Tutorials.html)
- **ROS2 Design**: [design.ros2.org](https://design.ros2.org/)
- **Community**: [discourse.ros.org](https://discourse.ros.org/)
- **GitHub**: [github.com/ros2](https://github.com/ros2)

---

## Best Practices

### **Development**
- Use **namespaces** for node organization
- Implement **proper error handling** and logging
- Follow **naming conventions** for topics and nodes
- Use **parameters** for configurable behavior
- Write **unit tests** for your nodes

### **Performance**
- Choose appropriate **QoS profiles** for your use case
- Monitor **system resources** and optimize accordingly
- Use **composition** for multiple nodes in single process
- Profile with **ros2 tools** for bottleneck identification

---

**Happy ROS2 Development! 🤖**