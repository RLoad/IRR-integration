# IRR-integration
## 0. to install and configure environment
clone the pub one with submoudel
```
git clone  --recurse-submodule git@gitlab.com:certh...
```
check the farther and submoule is in right branch, if not check out
```
git submodule init
git submodule update
```
docker build in the same level with catkin_ws, the docker main_pkg
```
docker compose build 
# or if want to rebuild everything
docker compose build --no-cache
```
run docker:
```
docker compose up -d
docker exec -it --user robetarme_user ros_noetic_t5.3_irr_control_development bash
```
in docker
```
catkin build -DCMAKE_BUILD_TYPE=Debug
```
first time will have problem about wp5-robotic-arms
```
sudo apt-get remove gcc g++
```
then in ```~/catkin_ws/src/wp5-robotic-arms/libs/ik-geo-cpp/rust-wrapper```:
```
cargo build
```

then after catkin build again, all function can be run as follow

## 1. first the code need run under the robetarme framework.
in folder ```/robetarme-irr-common/t5.3-irr-control/docker/main_pkg``` run this
```
# just run docker up
docker compose up -d
# Accessing docker in interactive mode from a shell
docker exec -it --user robetarme_user ros_noetic_t5.3_irr_control_development bash
```
## 2. run simulation or real robot
out side the docker, in folder /robetarme-irr-common/t5.3-irr-control/sup
run these for simulation
```
run_ur_cb_sim.sh # for a cb-series UR robot (robot without e arg)
run_ur_e_sim.sh # for a e-series UR robot
```
notice for the ur10e, the simulator should add ext control as follow:https://github.com/epfl-lasa/wp5-metal-additive-common/blob/main/ur-package/docker/e-series-docker/README.md
also load the program file
then turn on and start robot, but not click play here, you need get into docker env and run the sh first
## 3. In DOCKER env, run the  
build first if you change anything
```
catkin build -DCMAKE_BUILD_TYPE=Debug
#source devel/setup.bash
#source /opt/ros/noetic/setup.bash
```
run this to conect and control robot
```
sh run_docker_planner.sh
```
click play in simulation on robot side
run this to plan and move robot
```
sh run_docker_task_welding.sh
```
run this one to active laser
```
sh run_docker_laser_service.sh 
```
to acitvate the controller, need run
```
# source /opt/ros/noetic/setup.bash
rosservice call /ur10e/wp5_task_node/execution/enable "{}" # for the ur10e robot usage
rosservice call /ur5/wp5_task_node/execution/enable "{}" # for the ur5 robot usage
```

## 4 Notice:
switch between real robot and simulation, change this line in wp5_mam_planner_node.launch
```
<arg name="simu" doc="Simulation or real robot : [ON, OFF]" default="ON" />
```
if want to use real vision feedback, in ```mam_task.launch```, change the ```toy_waypoint``` to ```false```