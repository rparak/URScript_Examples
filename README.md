# URScript_Examples

## Requirements:

**Software:**
```bash
Universal Robots Polyscope
```

| Software/Package           | Link                                                                                  |
| -------------------------- | ------------------------------------------------------------------------------------- |
| Universal Robots Polyscope | https://www.universal-robots.com/download/                                            |

## Circular Motion:

**Description:**

A simple program to demonstrate circular motion. The user selects the initialization position (Waipoint_Home) and the radius (v_radius) in meters. Then just run the program.

The program was tested with Universal Robots UR10e robot (on the latest version of UR Polyscope). 

**Universal Robots UR10e - Simulation:**
<p align="center">
 <img src="https://github.com/rparak/URScript_Examples/blob/main/images/move_circle_ursim.PNG" width="600" height="400">
</p>

**URScript Code:**
```bash
 Program
   BeforeStart
     MoveJ
       Waypoint_Home
     v_radius≔0.050
     v_point_1≔p[Waypoint_Home[0] + v_radius,Waypoint_Home[1],Waypoint_Home[2],Waypoint_Home[3],Waypoint_Home[4],Waypoint_Home[5]]
     v_point_2≔p[Waypoint_Home[0],Waypoint_Home[1] + v_radius,Waypoint_Home[2],Waypoint_Home[3],Waypoint_Home[4],Waypoint_Home[5]]
     v_point_3≔p[Waypoint_Home[0] - v_radius,Waypoint_Home[1],Waypoint_Home[2],Waypoint_Home[3],Waypoint_Home[4],Waypoint_Home[5]]
     v_point_4≔p[Waypoint_Home[0],Waypoint_Home[1] - v_radius,Waypoint_Home[2],Waypoint_Home[3],Waypoint_Home[4],Waypoint_Home[5]]
   Robot Program
     MoveP
       v_point_1
       CircleMove
         v_point_2
         v_point_3
       CircleMove
         v_point_4
         v_point_1
```

## Modular Pick & Place:

**Description:**

A simple program to demonstrate a modular pick and place application. The user selects the initialization position (Waypoint_Home), number of objects (v_count_of_obj), mode (v_inv_mode), etc. The application mode can be inverse or non-inverse. Inverse mode means that the robot moves cyclically and transfers objects from side to side. The non-inverse mode just transfers the objects to the final position.

This application can be used as a demonstration for beginners, kids, etc. to show how easy it is to program a collaborative robot.

The program was tested with Universal Robots UR10e robot and Robotiq gripper (on the latest version of UR Polyscope). 

**Universal Robots UR10e:**
<p align="center">
 <img src="https://github.com/rparak/URScript_Examples/blob/main/images/pick_and_place_rw.png" width="600" height="450">
</p>

**Universal Robots UR10e - Simulation:**
<p align="center">
 <img src="https://github.com/rparak/URScript_Examples/blob/main/images/pick_and_place_ursim.PNG" width="600" height="400">
</p>

**URScript Code:**
```bash
 Program
   BeforeStart
     v_count_of_obj≔3
     v_obj_height≔0.030
     v_actual_state≔1
     v_inv_mode≔ False 
     v_counter≔0
     v_pick_offset≔p[0.0,0.0,0.0,0.0,0.0,0.0]
     v_place_offset≔p[0.0,0.0,0.0,0.0,0.0,0.0]
     v_pick_z≔0.2
     v_place_z≔0.2
     Point_Place[2]=0.415+v_place_z
     Point_Pick[2]=0.415+v_pick_z
     v_rpy_vec_pick≔rotvec2rpy([Point_Pick[3],Point_Pick[4],Point_Pick[5]])
     v_rot_vec_pick≔rpy2rotvec([3.142,0.0,v_rpy_vec_pick[2]])
     Point_Pick[3]=v_rot_vec_pick[0]
     Point_Pick[4]=v_rot_vec_pick[1]
     Point_Pick[5]=v_rot_vec_pick[2]
     v_rpy_vec_place≔rotvec2rpy([Point_Place[3],Point_Place[4],Point_Place[5]])
     v_rot_vec_place≔rpy2rotvec([3.142,0.0,v_rpy_vec_place[2]])
     Point_Place[3]=v_rot_vec_place[0]
     Point_Place[4]=v_rot_vec_place[1]
     Point_Place[5]=v_rot_vec_place[2]
     Gripper Reset and Activate
     Gripper Open (1)
     MoveJ
       Waypoint_Home
   Robot Program
     Switch v_actual_state
       Case 1
         If v_inv_mode== True 
           v_actual_state≔3
         Else
           v_actual_state≔2
       Case 2
         If v_counter<v_count_of_obj
           MoveL
             Point_Pick
             v_pick_offset≔pose_trans(Point_Pick, p[0.0,0.0,v_pick_z-((v_count_of_obj-(v_counter+1))*v_obj_height),0.0,0.0,0.0])
             v_pick_offset
             Gripper Close (1)
             Point_Pick
             Point_Place
             v_place_offset≔pose_trans(Point_Place, p[0.0,0.0,v_place_z-(v_counter*v_obj_height),0.0,0.0,0.0])
             v_place_offset
             Gripper Open (1)
             Point_Place
             v_counter=v_counter+1
         Else
           MoveJ
             Waypoint_Home
           Halt
       Case 3
         If v_counter<v_count_of_obj
           MoveL
             Point_Pick
             v_pick_offset≔pose_trans(Point_Pick, p[0.0,0.0,v_pick_z-((v_count_of_obj-(v_counter+1))*v_obj_height),0.0,0.0,0.0])
             v_pick_offset
             Gripper Close (1)
             Point_Pick
             Point_Place
             v_place_offset≔pose_trans(Point_Place, p[0.0,0.0,v_place_z-(v_counter*v_obj_height),0.0,0.0,0.0])
             v_place_offset
             Gripper Open (1)
             Point_Place
             v_counter=v_counter+1
         Else
           v_actual_state≔4
       Case 4
         If v_counter>0
           MoveL
             Point_Place
             v_pick_offset≔pose_trans(Point_Place, p[0.0,0.0,v_place_z-((v_counter-1)*v_obj_height),0.0,0.0,0.0])
             v_pick_offset
             Gripper Close (1)
             Point_Place
             Point_Pick
             v_place_offset≔pose_trans(Point_Pick, p[0.0,0.0,v_pick_z-((v_count_of_obj-v_counter)*v_obj_height),0.0,0.0,0.0])
             v_place_offset
             Gripper Open (1)
             Point_Pick
             v_counter=v_counter-1
         Else
           v_actual_state≔3

```

## Contact Info:
Roman.Parak@outlook.com

## License
[MIT](https://choosealicense.com/licenses/mit/)
