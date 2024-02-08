# Instructions

## step 0: get LiDAR ROS2 Package
```bash
cd ~

mkdir -p ldlidar_ros2_ws/src

cd ldlidar_ros2_ws/src

git clone -b design3 https://github.com/norlab-ulaval/ldlidar_ros2.git
git submodule update --init --recursive
```
## step 1: system setup
- Connect the LiDAR to your system motherboard via an onboard serial port or usB-to-serial module (for example, CP2102 module).

- Set the -x permission for the serial port device mounted by the radar in the system (for example, /dev/ttyUSB0)

  - In actual use, the LiDAR can be set according to the actual mounted status of your system, you can use 'ls -l /dev' command to view.

``` bash
cd ~/ldlidar_ros2_ws

sudo chmod 777 /dev/ttyUSB0
```
- Modify the `port_name` value in the Lanuch file corresponding to the radar product model under `launch/`, using `ld14.launch.py` as an example, as shown below.

```py
#!/usr/bin/env python3
from launch import LaunchDescription
from launch_ros.actions import Node

'''
Parameter Description:
---
- Set laser scan directon: 
  1. Set counterclockwise, example: {'laser_scan_dir': True}
  2. Set clockwise,        example: {'laser_scan_dir': False}
- Angle crop setting, Mask data within the set angle range:
  1. Enable angle crop fuction:
    1.1. enable angle crop,  example: {'enable_angle_crop_func': True}
    1.2. disable angle crop, example: {'enable_angle_crop_func': False}
  2. Angle cropping interval setting:
  - The distance and intensity data within the set angle range will be set to 0.
  - angle >= 'angle_crop_min' and angle <= 'angle_crop_max' which is [angle_crop_min, angle_crop_max], unit is degress.
    example:
      {'angle_crop_min': 135.0}
      {'angle_crop_max': 225.0}
      which is [135.0, 225.0], angle unit is degress.
'''

def generate_launch_description():
  # LDROBOT LiDAR publisher node
  ldlidar_node = Node(
      package='ldlidar_ros2',
      executable='ldlidar_ros2_node',
      name='ldlidar_publisher_ld14',
      output='screen',
      parameters=[
        {'product_name': 'LDLiDAR_LD14'},
        {'laser_scan_topic_name': 'scan'},
        {'point_cloud_2d_topic_name': 'pointcloud2d'},
        {'frame_id': 'base_laser'},
        {'port_name': '/dev/ttyUSB0'},
        {'serial_baudrate' : 115200},
        {'laser_scan_dir': True},
        {'enable_angle_crop_func': False},
        {'angle_crop_min': 135.0},
        {'angle_crop_max': 225.0}
      ]
  )

  # base_link to base_laser tf node
  base_link_to_laser_tf_node = Node(
    package='tf2_ros',
    executable='static_transform_publisher',
    name='base_link_to_base_laser_ld14',
    arguments=['0','0','0.18','0','0','0','base_link','base_laser']
  )


  # Define LaunchDescription variable
  ld = LaunchDescription()

  ld.add_action(ldlidar_node)
  ld.add_action(base_link_to_laser_tf_node)

  return ld
```

## step 2: build

Run the following command.

```bash
cd ~/ldlidar_ros2_ws

colcon build
```
## step 3: run

### step3.1: package environment variable settings

- After the compilation is completed, you need to add the relevant files generated by the compilation to the environment variables, so that the ROS environment can recognize them. The execution command is as follows. This command is to temporarily add environment variables to the terminal, which means that if you reopen a new terminal, you also need to re-execute it. The following command.
  
  ```bash
  cd ~/ldlidar_ros2_ws
  
  source install/local_setup.bash
  ```
  
- In order to never need to execute the above command to add environment variables after reopening the terminal, you can do the following.

  ```bash
  echo "source ~/ldlidar_ros2_ws/install/local_setup.bash" >> ~/.bashrc
  
  source ~/.bashrc
  ```
### step3.2: start LiDAR node

- The product is LDROBOT LiDAR LD14
  - start ld14 lidar node:
  ``` bash
  ros2 launch ldlidar_ros2 ld14.launch.py
  ```
  - start ld14 lidar node and show on the Rviz2:
  ``` bash
  ros2 launch ldlidar_ros2 viewer_ld14.launch.py
  ```

- The product is LDROBOT LiDAR LD14P
  - start ld14p lidar node:
  ``` bash
  ros2 launch ldlidar_ros2 ld14p.launch.py
  ```
  - start ld14p lidar node and show on the Rviz2:
  ``` bash
  ros2 launch ldlidar_ros2 viewer_ld14p.launch.py
  ```

- The product is LDROBOT LiDAR LD06
  - start ld06 lidar node:
  ``` bash
  ros2 launch ldlidar_ros2 ld06.launch.py
  ```
  - start ld06 lidar node and show on the Rviz2:
  ``` bash
  ros2 launch ldlidar_ros2 viewer_ld06.launch.py
  ```

- The product is LDROBOT LiDAR LD19
  - start ld19 lidar node:
  ``` bash
  ros2 launch ldlidar_ros2 ld19.launch.py
  ```
  - start ld19 lidar node and show on the Rviz2:
  ``` bash
  ros2 launch ldlidar_ros2 viewer_ld19.launch.py
  ```

- The product is LDROBOT LiDAR STL-19P
  - start ld19 lidar node:
  ``` bash
  ros2 launch ldlidar_ros2 stl19p.launch.py
  ```
  - start ld19 lidar node and show on the Rviz2:
  ``` bash
  ros2 launch ldlidar_ros2 viewer_stl19p.launch.py
  ```

## step 4: Data visualization

> The code supports ubuntu 20.04 ros2 foxy version and above, using rviz2 visualization.

- new a terminal (Ctrl + Alt + T) and use Rviz2 tool(run command: `rviz2`) ,open the `ldlidar.rviz` file below the rviz2 folder of the readme file directory
```bash
rviz2
```
