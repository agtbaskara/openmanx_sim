# openmanx_sim
Repository for ROS + Gazebo + Moveit welding inspection robot simulation, Based on ROBOTIS OpenManipulator-X

Example video: https://youtu.be/3dkJmaKnN4o

## Software Requirment:
- Ubuntu (Tested on Ubuntu 20.04)
- ROS (Tested on ROS Noetic)
- catkin-tools (https://catkin-tools.readthedocs.io/)

## Usage Guide
- Clone this repo to `catkin_ws/src` (Use `--recursive` to also clone submodules)
- Build ROS workspace using catkin-tools
- Run gazebo
	```bash
	roslaunch openmanx_moveit_config gazebo.launch
	```
- Run moveit move_group controller
	```bash
	roslaunch openmanx_moveit_config move_group.launch
	```
- Run rviz (Optional)
	```bash
	roslaunch openmanx_moveit_config moveit_rviz.launch
	```
- Run this to spawn welding sample
	```bash
	rosrun gazebo_ros spawn_model -urdf -file `rospack find openmanx_description`/weldingsample/robot.urdf -model weldingsample -x 0.3 -y 0 -z 0 -R 0 -P 0 -Y -1.5708
	```

## How to Replicate
- Export Onshape to URDF

	Use onshape-to-robot (https://github.com/Rhoban/onshape-to-robot)

	Link to Onshape File:
	- OpenManipulator 5-DOF (Used in this repo)
		https://cad.onshape.com/documents/28c26969b0b08f61f45f14e2/w/00602f3f87a6b6b7e5dae642/e/e45affbdf984856d06f2d263
	- OpenManipulator 6-DOF
		https://cad.onshape.com/documents/83dafbde1bc11161915b00a7/w/d38950a5e3b91b550355d617/e/0623a09f409143684d22b968
	- WeldingSample (for spawning)
		https://cad.onshape.com/documents/b92d593135b6184c78e7ec7c/w/0f873dd310687a8e6892cb5d/e/9a378e814d7ea7f423dd5de1

- Create new ros package:

	Navigate to `catkin_ws/src`

	```bash
	catkin_create_pkg openmanx_description std_msgs rospy roscpp
	```

- Create `robots` folder
	
	Copy converted URDF and STL files to the folder

- Modify URDF
	- Change robot name from `onshape` to `openmanx`
	- Add world link and world joint to `robots.urdf`
		```xml
		<link name="world" />
		<joint name="world_to_base_link" type="fixed">
		<parent link="world" />
		<child link="base" />
		</joint>
		```
	- Modify all mesh file path in the urdf to ros package path

		Example:
		```xml
		<mesh filename="package://dc12_std.stl"/>
		```
		To
		```xml
		<mesh filename="package://openmanx_description/robots/dc12_std.stl"/>
		```

- Build workspace
	```bash
	catkin build
	source devel/setup.bash
	```

- Create New Moveit Configuration Package
	- Launch moveit setup assistant
		```bash
		roslaunch moveit_setup_assistant setup_assistant.launch
		```
	- Load the `robot.urdf` at `openmanx_description/robots/robot.urdf`

	- Continue the setup assistant (follow the guide on https://github.com/lFatality/ros_moveit_gazebo_ws)

	- Build workspace
		```bash
		catkin build
		source/devel/setup.bash
		```

- Modify moveit configuration
	- Add this to `openmanx_moveit_controller_manager.launch.xml`
		```xml
		<arg name="execution_type" default=""/>
		```

## Reference:
- J. Cacace and L. Joseph, Mastering ROS for robotics Programming, Second EDITION: Design, build, and simulate complex robots using the Robot operating System, 2nd edition. Packt Publishing, 2018.
- Moveit Tutorials, https://ros-planning.github.io/moveit_tutorials/
- ROS1 Moveit Gazebo URDF Example, https://github.com/lFatality/ros_moveit_gazebo_ws
- ROS Tutorial - Simulation of Robotic Arm Using Gazebo , MoveIt and RViz, https://www.youtube.com/watch?v=O7nBa7mnfW4
- ROBOTIS OpenManipulator-X documentation, https://emanual.robotis.com/docs/en/platform/openmanipulator_x/overview/
- onshape-to-robot documentation, https://onshape-to-robot.readthedocs.io/en/latest/
- Onshape to robot tutorial, https://www.youtube.com/watch?v=C8oK4uUmbRw
