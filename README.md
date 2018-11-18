# ghost_gazebo
ROS package for the simulation of Ghost Robotics platforms using Gazebo

## General notes
1. This ROS package is used in conjunction with and included in the `thirdparty` folder of the Ghost Robotics SDK artifacts, which are independently distributed by Ghost Robotics.
2. At this point, there is no open source Vision60 control example in the SDK, but once released the following procedure should be identical. For now, you can just launch the Vision60 simulation as explained below.
3. The simulation depends a lot on friction parameters. These parameters can be configured in the URDF xacro files ([minitaur_gazebo.urdf.xacro](minitaur_description/urdf/minitaur_gazebo.urdf.xacro) and [vision60_gazebo.urdf.xacro](vision60_description/urdf/vision60_gazebo.urdf.xacro)).

## Running the simulation
1. Copy the package to your ROS workspace folder
```
$ cp -r <artifacts_location>/thirdparty/ghost_gazebo <catkin_ws_location>/src
```
2. The following line needs to be added in ~/.bashrc to allow for proper Gazebo model detection
```
export GAZEBO_MODEL_PATH=<catkin_ws_location>/src/ghost_gazebo/minitaur_description/sdf:<catkin_ws_location>/src/ghost_gazebo/minitaur_description:<catkin_ws_location>/src/ghost_gazebo/vision60_description
```
3. Configure [ghost.world](gazebo_scripts/worlds/ghost.world) to include anything you want to show up in your simulation. In this file, you can also configure `max_step_size` and `real_time_update_rate` for the simulation. Usually a `max_step_size` of 0.0003 works for Minitaur. Vision needs a smaller value for contact forces (0.00005 should be enough). Currently, `real_time_update_rate` is configured to 2000Hz.
4. To launch the simulation, build the package and, depending on the robot, run:
```
$ roslaunch gazebo_scripts minitaur_gazebo.launch
```
or 
```
$ roslaunch gazebo_scripts vision60_gazebo.launch
```
5. Compile and run the corresponding Ghost Robotics SDK script to control the robot. For this, please use the [Makefile example](extras/Makefile) provided here (the format might change in the future). The simulation exposes 3 ROS topics for control: `/behaviorId`, `/behaviorMode` and `/twist`, and several others for checking the robot state: `/<robot_name>/state/imu`, `/<robot_name>/state/batteryState`, `/<robot_name>/state/behaviorId`, `/<robot_name>/state/behaviorMode`, `/<robot_name>/state/joint`, `/<robot_name>/state/pose`. As an example following the Ghost Robotics SDK FirstHop example for Minitaur, the overall process should look like that:
```
$ mv <artifacts_location>/examples/FirstHop/Makefile <artifacts_location>/examples/FirstHop/Makefile.bk
$ cp <artifacts_location>/thirdparty/ghost_gazebo/extras/Makefile <artifacts_location>/examples/FirstHop/Makefile
$ cd <artifacts_location>/examples/FirstHop
$ make
$ ./FirstHop_MINITAUR_E
```

## Converting Minitaur's URDF to SDF
The most important requirement to setup the simulation is to properly convert the Minitaur's URDF file to an SDF file. We have a URDF xacro file ([minitaur_gazebo.urdf.xacro](minitaur_description/urdf/minitaur_gazebo.urdf.xacro)) in the [urdf folder](minitaur_description/urdf) and properly converted URDF and SDF files. If you need to modify the xacro file, please run the following commands to convert the xacro file to URDF
```bash
$ python xacro.py minitaur_gazebo.urdf.xacro > minitaur_gazebo.urdf
```
and subsequently to SDF using
```bash
$ gz sdf -p minitaur_gazebo.urdf > minitaur_gazebo.sdf
```

However, you also need to do the following after the conversion:
1. Make sure to add the lines below to the end of the SDF file (before the model termination). These lines realize the 5-bar mechanism constraint for each leg.
```
    <joint name='constraint_back_left' type='revolute'>
      <parent>lower_leg_back_leftL_link</parent>
      <child>lower_leg_back_leftR_link</child>
      <pose frame=''>0 0 0.2 0 -0 0</pose>
      <axis>
        <xyz>0 1 0</xyz>
        <use_parent_model_frame>0</use_parent_model_frame>
        <limit>
          <lower>-1.79769e+308</lower>
          <upper>1.79769e+308</upper>
          <effort>-1</effort>
          <velocity>-1</velocity>
        </limit>
        <dynamics>
          <spring_reference>0</spring_reference>
          <spring_stiffness>0</spring_stiffness>
          <damping>0</damping>
        </dynamics>
      </axis>
      <physics>
        <ode>
          <limit>
            <cfm>0</cfm>
            <erp>0.2</erp>
          </limit>
          <suspension>
            <cfm>0</cfm>
            <erp>0.2</erp>
          </suspension>
        </ode>
      </physics>
    </joint>
    <joint name='constraint_back_right' type='revolute'>
      <parent>lower_leg_back_rightR_link</parent>
      <child>lower_leg_back_rightL_link</child>
      <pose frame=''>0 0 0.2 0 -0 0</pose>
      <axis>
        <xyz>-0 -1 -0</xyz>
        <use_parent_model_frame>0</use_parent_model_frame>
        <limit>
          <lower>-1.79769e+308</lower>
          <upper>1.79769e+308</upper>
          <effort>-1</effort>
          <velocity>-1</velocity>
        </limit>
        <dynamics>
          <spring_reference>0</spring_reference>
          <spring_stiffness>0</spring_stiffness>
          <damping>0</damping>
        </dynamics>
      </axis>
      <physics>
        <ode>
          <limit>
            <cfm>0</cfm>
            <erp>0.2</erp>
          </limit>
          <suspension>
            <cfm>0</cfm>
            <erp>0.2</erp>
          </suspension>
        </ode>
      </physics>
    </joint>
    <joint name='constraint_front_left' type='revolute'>
      <parent>lower_leg_front_leftL_link</parent>
      <child>lower_leg_front_leftR_link</child>
      <pose frame=''>0 0 0.2 0 -0 0</pose>
      <axis>
        <xyz>0 1 0</xyz>
        <use_parent_model_frame>0</use_parent_model_frame>
        <limit>
          <lower>-1.79769e+308</lower>
          <upper>1.79769e+308</upper>
          <effort>-1</effort>
          <velocity>-1</velocity>
        </limit>
        <dynamics>
          <spring_reference>0</spring_reference>
          <spring_stiffness>0</spring_stiffness>
          <damping>0</damping>
        </dynamics>
      </axis>
      <physics>
        <ode>
          <limit>
            <cfm>0</cfm>
            <erp>0.2</erp>
          </limit>
          <suspension>
            <cfm>0</cfm>
            <erp>0.2</erp>
          </suspension>
        </ode>
      </physics>
    </joint>
    <joint name='constraint_front_right' type='revolute'>
      <parent>lower_leg_front_rightR_link</parent>
      <child>lower_leg_front_rightL_link</child>
      <pose frame=''>0 0 0.2 0 -0 0</pose>
      <axis>
        <xyz>-0 -1 -0</xyz>
        <use_parent_model_frame>0</use_parent_model_frame>
        <limit>
          <lower>-1.79769e+308</lower>
          <upper>1.79769e+308</upper>
          <effort>-1</effort>
          <velocity>-1</velocity>
        </limit>
        <dynamics>
          <spring_reference>0</spring_reference>
          <spring_stiffness>0</spring_stiffness>
          <damping>0</damping>
        </dynamics>
      </axis>
      <physics>
        <ode>
          <limit>
            <cfm>0</cfm>
            <erp>0.2</erp>
          </limit>
          <suspension>
            <cfm>0</cfm>
            <erp>0.2</erp>
          </suspension>
        </ode>
      </physics>
    </joint>
```
2. For each leg, rename all the toe collision descriptions to match the corresponding toe (e.g., for the front left leg, rename `lower_leg_front_leftL_link_fixed_joint_lump__toe0_collision_1` to `toe0_collision`), and also add the following lines immediately below this collision tag (change `0` to match each toe):
```
<sensor name="toe0_contact" type="contact">
    <plugin name="toe0_plugin" filename="libcontact.so"/>
    <contact>
        <collision>toe0_collision</collision>
    </contact>
</sensor>
```
3. Make sure to move the SDF file to the [minitaur_constrained folder](minitaur_description/sdf/minitaur_constrained) and rename it from minitaur_gazebo.sdf to minitaur_constrained.sdf.
4. Rename the model name in [minitaur_constrained.sdf](minitaur_description/sdf/minitaur_constrained/minitaur_constrained.sdf) to `minitaur_constrained`. Do the same thing for the namespace in `libgazebo_ros_control.so`.

## Converting Vision60's URDF to SDF
This process is more straightforward and we have already included properly converted URDF and SDF files. We have a URDF xacro file ([vision60_gazebo.urdf.xacro](vision60_description/urdf/vision60_gazebo.urdf.xacro)) in the urdf folder that can be converted to URDF with
```bash
$ python xacro.py vision60_gazebo.urdf.xacro > vision60_gazebo.urdf
```
and subsequently to SDF using
```bash
$ gz sdf -p vision60_gazebo.urdf > ../sdf/vision60.sdf
```

After the conversion:
1. For each leg, rename all the toe collision descriptions to match the corresponding toe (use `toe0_collision`, `\toe1_collision` as in the Minitaur SDF), and also add the following lines immediately below this collision tag (change `0` to match each toe):
```
<sensor name="toe0_contact" type="contact">
    <plugin name="toe0_plugin" filename="libcontact.so"/>
    <contact>
        <collision>toe0_collision</collision>
    </contact>
</sensor>
```
2. Make sure to move the SDF file to the [vision60 SDF folder](vision60_description/sdf) and rename it to vision60.sdf.