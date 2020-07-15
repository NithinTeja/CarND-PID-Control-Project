# CarND-Controls-PID
Self-Driving Car Engineer Nanodegree Program

---
# Rubrics

## Compilation 
Code is successfully compiled using Visual Studio. I did this project on my local machine using VS 2017

## Implementation
The PID procedure follows what was taught in the lessons. All the hyperparmaters/PID (proportional, Integral and Differential) gains were manually tuned starting with P gain as shown in the class. The total error is calculated by adding the all three error terms i.e. P, I and D errors.

## Reflection
**Describe the effect each of the P, I, D components had in your implementation.**
- Initially all the gains were set to 0 to see how the vehicle moves and its behavior. As expected, the vehicle goes straight leaving the lane and CTE (cross-track error) goes high. This is because P,I,D gains were not tuned to reduce CTE. Here's the [video](Videos/P0_I0_D0) which shows this effect.
- In the next step, P gain was increased to `0.5`. The vehicle started to move and stay within the lane for first **15** seconds. Later, it started to oscillate around by leaving the lane. This is due to higher P gain which makes the vehicle to [oscillate](Videos/P_0p5_I0_D0) without settling at the center of the lane. [Decreasing](Videos/P_0p15_I0_D0) the P-gain to `0.15` helped a little but P-term alone will not make the vehicle stay in the lane. We need to add a term that reduce CTE over the time.
- As shown in the class, D-term is introduced along with P-term. This will reduce CTE and helps the vehicle to settle around center of the lane by minimizing the oscillations. Initially, D-gain is set to `0.5` by keeping P-gain as `0.15`. This helped the vehicle to travel around the track but D-term is not strong enough to minimize the oscillations. This [video](Videos/P_0p15_I0_D0p5) shows that vehicle is leaving the edges of the lane. Increasing the D-gain to `3.0` solved this issue. Vehicle successfully travelled around the lap.I adjusted default throttle value from `0.3` to `0.4` to increase the vehicle speed and complete the lap faster. I also tracked Total CTE around the lap i.e. sum of absolute CTE for each time step. This [video](Videos/P_0p15_I0_D3p0_LapCTE_915) has Total CTE of `915` with P-gain `0.15` and I-gain  `3.0`
- Next, I introduced I-term to reduce the total CTE and also minimize the error when the vehicle is cornering. Initially I set I-gain to `0.05` by keeping P-gain `0.15` and D-gain `3.0`. I found that vehicle is [spinning](Videos/P_0p15_I0p05_D3p0) around. The main function of integral term is to reduce the cummulative error over the time. I reduced the I-gain by 10 times i.e. set to `0.001` and this made the vehicle to successfully travel around the lap without leaving the edges. The total CTE with these gains is `698` which is less than the previous PD controller. Here's the [video](P_0p15_I_0p001_D3p0_LapCTE_698_Th_0p4) with all the three gains.
-Finally, I also tried to play around with throttle controller. My goal is to make the vehicle travel at constant **40 mph** by adjusting the throttle and steering. I took the error between target_speed i.e. 40 and actual speed from the simulator. I fed this error to PID controller and calculated the throttle value. I manually adjusted PID gains for throttle controller as well. This [video](Videos/P_0p15_I_0p001_D2p5_Pth_0p15_Ith0p0005_Dth3p0_LapCTE_630_TargetSpd_40) shows car driving the complete lap at constant 40 mph using 2 PID controllers i.e 1 for each Steering and Throttle controller. The total CTE for the lap using two PID controllers is ~ `630` which is better than the total CTE we got before `698`using 1 PID controller.

**Describe how the final hyperparameters were chosen.**
The final gains using two PID controllers are as follows:
- **Steering PID Controller** 
    - P gain `0.15`
    - I gain `0.001`
    - D gain `2.5`
- **Throttle PID Controller**
    - P gain `0.15`
    - I gain `0.0005`
    - D gain `3.0`

## Dependencies

* cmake >= 3.5
 * All OSes: [click here for installation instructions](https://cmake.org/install/)
* make >= 4.1(mac, linux), 3.81(Windows)
  * Linux: make is installed by default on most Linux distros
  * Mac: [install Xcode command line tools to get make](https://developer.apple.com/xcode/features/)
  * Windows: [Click here for installation instructions](http://gnuwin32.sourceforge.net/packages/make.htm)
* gcc/g++ >= 5.4
  * Linux: gcc / g++ is installed by default on most Linux distros
  * Mac: same deal as make - [install Xcode command line tools]((https://developer.apple.com/xcode/features/)
  * Windows: recommend using [MinGW](http://www.mingw.org/)
* [uWebSockets](https://github.com/uWebSockets/uWebSockets)
  * Run either `./install-mac.sh` or `./install-ubuntu.sh`.
  * If you install from source, checkout to commit `e94b6e1`, i.e.
    ```
    git clone https://github.com/uWebSockets/uWebSockets 
    cd uWebSockets
    git checkout e94b6e1
    ```
    Some function signatures have changed in v0.14.x. See [this PR](https://github.com/udacity/CarND-MPC-Project/pull/3) for more details.
* Simulator. You can download these from the [project intro page](https://github.com/udacity/self-driving-car-sim/releases) in the classroom.

Fellow students have put together a guide to Windows set-up for the project [here](https://s3-us-west-1.amazonaws.com/udacity-selfdrivingcar/files/Kidnapped_Vehicle_Windows_Setup.pdf) if the environment you have set up for the Sensor Fusion projects does not work for this project. There's also an experimental patch for windows in this [PR](https://github.com/udacity/CarND-PID-Control-Project/pull/3).

## Basic Build Instructions

1. Clone this repo.
2. Make a build directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./pid`. 

Tips for setting up your environment can be found [here](https://classroom.udacity.com/nanodegrees/nd013/parts/40f38239-66b6-46ec-ae68-03afd8a601c8/modules/0949fca6-b379-42af-a919-ee50aa304e6a/lessons/f758c44c-5e40-4e01-93b5-1a82aa4e044f/concepts/23d376c7-0195-4276-bdf0-e02f1f3c665d)

## Editor Settings

We've purposefully kept editor configuration files out of this repo in order to
keep it as simple and environment agnostic as possible. However, we recommend
using the following settings:

* indent using spaces
* set tab width to 2 spaces (keeps the matrices in source code aligned)

## Code Style

Please (do your best to) stick to [Google's C++ style guide](https://google.github.io/styleguide/cppguide.html).

## Project Instructions and Rubric

Note: regardless of the changes you make, your project must be buildable using
cmake and make!

More information is only accessible by people who are already enrolled in Term 2
of CarND. If you are enrolled, see [the project page](https://classroom.udacity.com/nanodegrees/nd013/parts/40f38239-66b6-46ec-ae68-03afd8a601c8/modules/f1820894-8322-4bb3-81aa-b26b3c6dcbaf/lessons/e8235395-22dd-4b87-88e0-d108c5e5bbf4/concepts/6a4d8d42-6a04-4aa6-b284-1697c0fd6562)
for instructions and the project rubric.

## Hints!

* You don't have to follow this directory structure, but if you do, your work
  will span all of the .cpp files here. Keep an eye out for TODOs.

## Call for IDE Profiles Pull Requests

Help your fellow students!

We decided to create Makefiles with cmake to keep this project as platform
agnostic as possible. Similarly, we omitted IDE profiles in order to we ensure
that students don't feel pressured to use one IDE or another.

However! I'd love to help people get up and running with their IDEs of choice.
If you've created a profile for an IDE that you think other students would
appreciate, we'd love to have you add the requisite profile files and
instructions to ide_profiles/. For example if you wanted to add a VS Code
profile, you'd add:

* /ide_profiles/vscode/.vscode
* /ide_profiles/vscode/README.md

The README should explain what the profile does, how to take advantage of it,
and how to install it.

Frankly, I've never been involved in a project with multiple IDE profiles
before. I believe the best way to handle this would be to keep them out of the
repo root to avoid clutter. My expectation is that most profiles will include
instructions to copy files to a new location to get picked up by the IDE, but
that's just a guess.

One last note here: regardless of the IDE used, every submitted project must
still be compilable with cmake and make./

## How to write a README
A well written README file can enhance your project and portfolio.  Develop your abilities to create professional README files by completing [this free course](https://www.udacity.com/course/writing-readmes--ud777).

