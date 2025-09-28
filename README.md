# ros2_humble_help
A repository to take notes of tutorials references and steps

# rqt graph
plot a nodes graph

```bash
ros2 run rqt_graph rqt_graph
```

# turtlebot3 sim
env : windows 11, WSL ubuntu 22.04, ros2 humble

youtube video https://www.youtube.com/watch?v=idQb2pB-h2Q&t=1186s

official doc https://emanual.robotis.com/docs/en/platform/turtlebot3/simulation/#gazebo-simulation

installed : ros-humble-gazebo-*, ros-humble-cartographer, ros-humble-cartographer-ros, ros-humble-navigation2, ros-humble-nav2-bringup

 note `source /opt/ros/humble/setup.bash` throws the error `[ERROR] [gzclient-2]: process has died [pid 4048, exit code -6, cmd 'gzclient --gui-client-plugin=libgazebo_ros_eol_gui.so']` but gazebo-11 runs fine

added in `.bashrc`

```bash
export TURTLEBOT3_MODEL=waffle
export ROS_DOMAIN_ID=30 #TURTLEBOT3

source /opt/ros/humble/setup.bash
source /usr/share/gazebo-11/setup.sh
```

launch

- `ros2 launch turtlebot3_gazebo turtlebot3_world.launch.py`
- `ros2 run turtlebot3_teleop teleop_keyboard`
- `ros2 launch turtlebot3_cartographer cartographer.launch.py use_sim_time:=True`

save the map
- `ros2 run nav2_map_server map_saver_cli -f maps/my_map`

map_saver_cli produces pgm only, can be converted with imagemagick-6.q16 `convert my_map.pgm my_map.png`

now publish the map on a topic with the map server

`ros2 run nav2_map_server map_server --ros-args -p yaml_filename:=my_map.yaml`

to fix missing tf publish a dummy one :
`ros2 run tf2_ros static_transform_publisher 0 0 0 0 0 0 map base_link`

alternative method did not work
- fix warn in rviz "Global Status : Warn, Fixed Frame : No tf data. Actual error: Frame [map] does not exist" rmw has better discovery implementation that does not miss tf
- install `ros-humble-rmw-cyclonedds-cpp`
- export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
- in `/opt/ros/humble/share/turtlebot3_navigation2/params/waffle.yaml` make sure `robot_model_type: "nav2_amcl::DifferentialMotionModel"`

now navigate using the map
- `ros2 launch turtlebot3_navigation2 navigation2.launch.py use_sim_time:=True map:=~/turtlebot3_test_map/my_map.yaml`

