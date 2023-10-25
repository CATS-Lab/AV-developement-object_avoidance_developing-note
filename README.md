# Guideline for Object Avoidance in Autoware

## Introduction

**Key nodes for avoidance:**

`aster_avoid` and `velocity_set`.

**Working principle:** 

`velocity_set` will receive topic `safety_waypoints` to judge whether there is a barrier in the path. If there is a barrier, it will control the vehicle to decelerate or stop to avoid crash, which will publish to `finaobstacle_waypoint_pub_waypoints`. The place of barrier will publish to `obstacle_waypoint`.

`aster_avoid` will receive topic `obstacle_waypoint`. If there is a barrier, it will use astar algorithm to plan a new path, and publish to `safety_waypoints`.

Then, `velocity_set` will receive  `safety_waypoints` from `aster_avoid` and work iteratively until no barrier found. If user close the "Enable Avoidance" in `aster_avoid`, `velocity_set` will only stop the vehicle but not re-plan a new path. Make sure you have opened `aster_avoid` even if you don't need the re-plan function.

## Frequent problems

1. Planing conflict. Autoware has multiple path planing algorithms, such as `lattice_planner`, which may conflict when open more then one in the same time. Please close all related node about `lattice_planner` or other planing nodes when using `aster_avoid` and `velocity_set`.
2. Waypoint. The output of to node `pure_pursit` (which control the final speed) from `velocity_set` is `finaobstacle_waypoint_pub_waypoints`. Therefore, all speed output is depending on the waypoints information. Please open "Waypoint" in node `pure_pursit`.
3. Parameter setting. To make sure the vehicle can stop, set a high value to the following parameters:
   1. Stop Distance for Obstacle. The distance starting stop behavior between vehicle and obstacle. Reference value: 20-30 m.
   2. Decelerate Range. The distance starting deceleration behavior between vehicle and obstacle. There are some unknow questions in deceleration mode. Please set "" to 0 to ignore deceleration mode. 
   3. Detection Range.
   4. Detection Height Top/Bottom. Threshold to determine whether it is an obstacle.
   5. Deceleration for Obstacle. Maximum deceleration for avoidance. Reference value: 4 m/s.
   6. Velocity Change Limit. The limit of velocity change for avoidance. Reference value: 10 km/h.
   7. Temporal Waypoints Size.

![image-20231025102556240](mdPics/image-20231025102556240.png)
