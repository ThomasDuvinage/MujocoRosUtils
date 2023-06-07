# [MujocoRosUtils](https://github.com/isri-aist/MujocoRosUtils)
ROS-based MuJoCo utilities

## Install

### Requirements
- Compiler supporting C++17
- Tested with `Ubuntu 20.04 / ROS Noetic`

### Dependencies
- [MuJoCo](https://github.com/deepmind/mujoco) (>= 2.3.5)

### Installation procedure
```bash
# Setup catkin workspace.
$ mkdir -p ${HOME}/ros/ws_mujoco/src
$ cd ${HOME}/ros/ws_mujoco
$ wstool init src
$ wstool set -t src isri-aist/MujocoRosUtils git@github.com:isri-aist/MujocoRosUtils.git --git -y
$ wstool update -t src
# Install dependent packages.
$ source /opt/ros/${ROS_DISTRO}/setup.bash
$ rosdep install -y -r --from-paths src --ignore-src
# Build a package.
$ catkin init
$ catkin config --extend /opt/ros/${ROS_DISTRO}
$ catkin build mujoco_ros_utils -DCMAKE_BUILD_TYPE=RelWithDebInfo -DMUJOCO_ROOT_DIR=<absolute path to libtorch>
```
Add `source ${HOME}/ros/ws_mujoco/devel/setup.bash` to `${HOME}/.bashrc`.

## Plugin
### MujocoRosUtils::ClockPublisher
Plugin to publish clock topic.

All of the following attributes are optional.
- `topic_name`: Topic name of clock. (Default is `/clock`)
- `publish_rate`: Publish rate. (Default is 100.0 [Hz])
- `use_sim_time`: Value of `use_sim_time` rosparam. (Default is `true`)

An example of tags to be added to the MJCF file:
```xml
<extension>
  <plugin plugin="MujocoRosUtils::ClockPublisher"/>
</extension>
<worldbody>
  <plugin plugin="MujocoRosUtils::ClockPublisher">
    <config key="topic_name" value="/clock"/>
    <config key="publish_rate" value="100"/>
    <config key="use_sim_time" value="true"/>
  </plugin>
</worldbody>
```
This plugin must be registered in worldbody.

### MujocoRosUtils::PosePublisher
Plugin to publish topics or broadcast TF of pose and velocity of the body.

All of the following attributes are optional.
- `frame_id`: Frame ID of topics header or TF parent. (Default is `map`)
- `pose_topic_name`: Topic name of pose. (Default is `mujoco/<body name>/pose`)
- `vel_topic_name`: Topic name of velocity. (Default is `mujoco/<body name>/vel`)
- `publish_rate`: Publish rate. (Default is 30.0 [Hz])
- `output_tf`: Whether to broadcast TF. (Default is `false`)
- `tf_child_frame_id`: Child frame ID for TF. Used only when `output_tf` is `true`. (Default is `<body name>`)

An example of tags to be added to the MJCF file:
```xml
<extension>
  <plugin plugin="MujocoRosUtils::PosePublisher"/>
</extension>
<sensor>
  <plugin name="pose_publisher" plugin="MujocoRosUtils::PosePublisher" objtype="xbody" objname="object">
    <config key="frame_id" value="map"/>
    <config key="pose_topic_name" value="/pose"/>
    <config key="vel_topic_name" value="/vel"/>
    <config key="publish_rate" value="30"/>
    <config key="output_tf" value="false"/>
    <config key="tf_child_frame_id" value="object"/>
  </plugin>
</sensor>
```
The `objtype` attribute must be `xbody`.
