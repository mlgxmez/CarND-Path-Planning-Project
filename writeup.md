## Writeup Path Planning proect

[//]: # (Image References)

[image1]: img/best_distance.jpg "Crossing the threshold driving distance"
[image2]: img/best_distance2.jpg "Longest driven distance by the ego-car"

The Q&A walkthrough provided by the Udacity team is a good starting point to develop my final solution. Such as defining the trajectory of the car by using splines and find the most suitable moment to make a lane change. Before diving into this report, let me show a visual fact about the driving behavior of the car:

![alt text][image1]

The reviewer can check the solution in the `main.cpp` file.

### Valid trajectories

A valuable lesson taken from this project is to track as many variables as you can. Get their values in real-time. The first decision made was to instead of just measuring the distance between the ego-car and the car few meters forward, measure the distance between the ego-car and any car located in any of the three lanes:

```
struct cars
            {
              //bool spotted = false;
              int id;
              double vx;
              double vy;
              double check_speed;
              double check_car_s;
            };
            // 3 lanes: left, centre and right
            cars lanes[3];
```
Since you know the lane where the car is (just getting `sensor_fusion[i][6]`), all their state components in the `lanes` corresponding element.

The next decision was to save the previous *d* value of the ego-car. If the ego-car starts to make a lane change, since the value of `lane` variable may change quickly, I wanted its value to be constant while moving to another lane. So, unless the previous `d` value is not so far from the current `d` then the `lane` value cannot change.

Finally a set of conditions are implemented depending on which lane the ego-car is. The lane change behavior is only activated when the ego-car starts slowing down. An additional condition was included: `(lanes[0].check_car_s - car_s < -100)`, in this case if the car is in the leftmost lane and there is just a car on the same lane few meters ahead while the remaining lanes are empty then do the lane change. Before that, the ego-car would make the lane change if there were cars ahead of it which was completely a nonsense.

As the reviewer will check the code the ego-car is capable of:

* staying in its lane except for the time between lane changes.
* changing lanes

Depending on the traffic in the highway, some issues may appear such as:

* Unexpected lane changes of other cars while crossing the middle waypoints of the ego-car.
* Ego-car does not take into account other cars if they have the same `s` value.

Despite those uncontrollable issues the car is able drive safely for at least 4.32 miles as shown in the image below:

![alt text][image2]
