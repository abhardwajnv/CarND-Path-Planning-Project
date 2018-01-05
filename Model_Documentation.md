## CarND Path Planning Project

### Self Driving Car Engineering Nanodegree

---

The goals / steps of this project are the following:

* Compilation: Code must compile without errors with cmake and make.
* Implementation:
* The car is able to drive at least 4.32 miles without incident. The top right screen of the simulator shows the current/best miles driven without incident. Incidents include exceeding acceleration/jerk/speed, collision, and driving outside of the lanes. Each incident case is also listed below in more detail.
* The car drives according to the speed limit. The car does not drive faster than the speed limit. Also the car is not driving much slower than speed limit unless obstructed by traffic.
* Max Acceleration and Jerk are not Exceeded. The car does not exceed a total acceleration of 10 m/s^2 and a jerk of 10 m/s^3.
* Car does not have collisions. The car must not come into contact with any of the other cars on the road.
* The car stays in its lane, except for the time between changing lanes. The car does not spend more than a 3 second length out side the lane lanes during changing lanes, and every other time the car stays inside one of the 3 lanes on the right hand side of the road.
* The car is able to change lanes. The car is able to smoothly change lanes when it makes sense to do so, such as when behind a slower moving car and an adjacent lane is clear of other traffic.

[//]: # (Image References)
[image1]: ./output/full_run.png
[image2]: ./output/lane_switch1.png
[image3]: ./output/lane_switch2.png

---
## [Rubric](https://review.udacity.com/#!/rubrics/1020/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.

---
### Writeup / README

#### [Here](https://github.com/abhardwajnv/CarND-Path-Planning-Project/blob/master/Model_Documentation.md) is the writeup for this project.

You are reading it!

---

## Implementation

### 1. The car is able to drive at least 4.32 miles without incident.

With my current implementation Car is able to drive 21.60Miles in the simulator without any incidents.
Check below image for Reference:

![alt text][image1]


### 2. The car drives according to the speed limit. The car does not drive faster than the speed limit. Also the car is not driving much slower than speed limit unless obstructed by traffic.

The Car drives according to the speed limit discussed in the chapters and walkthrough of project.
The Car starts in Lane 1 with 1MPH speed and gradually speeds upto 49.5MPH if no obstructions are there.

Following is the code snippet from main.cpp showing initialization of lane and speed along with gradual increase of speed.

          int lane = 1;
          double ref_vel = 1.0; //mph
          ...
          ref_vel = min(ref_vel+.224, 49.5);


### 3. Max Acceleration and Jerk are not Exceeded. The car does not exceed a total acceleration of 10 m/s^2 and a jerk of 10 m/s^3.

Acceleration and Jerks does not exceed the specified limits for the full run of 21.60Miles in simulator.


### 4. Car does not have collisions. The car must not come into contact with any of the other cars on the road.

No Collisions observed upto 21.60Miles run while drive or lane switch.


### 5. The car stays in its lane, except for the time between changing lanes. The car does not spend more than a 3 second length out side the lane lanes during changing lanes, and every other time the car stays inside one of the 3 lanes on the right hand side of the road.

The car stays in its lane all the time for the simulator run. Car does not move to the left 3 incomng traffic lanes as well.
Also while lane switches car stays in the switched lane.


### 6. The car is able to change lanes. The car is able to smoothly change lanes when it makes sense to do so, such as when behind a slower moving car and an adjacent lane is clear of other traffic.

Following are the steps used for lane switching:

* First i am checking if the s-distance of the cars in same lane is less than 30m or not. If the distance is less than it checks for lane change if not the speed is increased gradually upto 49.5MPH
* If the distance becomes too close (i.e., less than 30M) car speed and lane are checked for lane switching descision.
* If the car is in lane 0 and the speed is less than 40MPH then the car will look for right change to Lane 1 if the lane is safe to switch.
* If the car is in Lane 2 and the speed is less than 40MPH then the car will look for left change to Lane 1 if the lane is safe to swtich.
* If the car is in Lane 1 and the speed is less than 40MPH then the car will look for right change to Lane 2 if the lane is safe to switch, if not then it checks for left change to Lane 0 if the lane is safe to switch.

Following is the code snippet from main.cpp showing the logic.

          if(too_close)
          {
            ref_vel -= .224;
            bool lane_switch = true;
            if(ref_vel < 40 && lane == 0)
            {
              lane_switch = estimate_switch(lane_switch, sensor_fusion, lane+1, prev_size, car_s);
              if(lane_switch)
              {
                lane+=1;
              }
            }
            else if(ref_vel < 40 && lane == 2)
            {
              lane_switch = estimate_switch(lane_switch, sensor_fusion, lane-1, prev_size, car_s);
              if(lane_switch)
              {
                lane-=1;
              }
            }
            else if(ref_vel < 40 && lane == 1)
            {
              lane_switch = estimate_switch(lane_switch, sensor_fusion, lane+1, prev_size, car_s);
              if(lane_switch)
              {
                lane+=1;
              }
              else
              {
                lane_switch = estimate_switch(lane_switch, sensor_fusion, lane-1, prev_size, car_s);
                if(!lane_switch)
                {
                  lane-=1;
                }
              }
            }
          }

Complete Code for this step can be found in main.cpp under main function.

Check below image reference for visualization of lane change.

![alt text][image2]
![alt text][image3]


## References:

* Project Walkthrough and Q&A 
* Referenced https://github.com/jeremy-shannon/CarND-Path-Planning-Project/blob/master/README.md for trajectory understanding.
