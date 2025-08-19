# Controlling the UnitreeG1edu

General objective: make a viable product that can interact with its surroundings in order to serve as a patrol device.

Precise objective: control a UnitreeG1edu device on an enclosed enviroment, with well behaved floors and walls, with a limited supply os obstacles to be catalogued, avoided and interacted with.

---

Requirements of the project

- To make a human machine interface (HMI) in order to have control of the robot's general behaviour, as well as monitor its state. 

- Compute all data on-site, as in oposition of sending data outside of the facility in which the device is on.

- Make it robust to outside perturbations.

## Information flow and control machinary

### General information flow

![alt text](general_diagram.png)

In the image, the Control Unit stands for a device that is capable of running the calculations of the robot's control.

The HMI part is solely the interface in which the overseer of the robot inputs their desire of behaviour and monitors the present and projected future states of the robot, as well as the layer of methods used to project paths and update the locations on obstacles.


### Deeper dive in the control unit

![alt text](control_unit.png)

Tere are two distinct parts on the control unit: deciding the behaviour and recognizing objects around the robot.

#### Recognizing objects

Given the RGB data provided, it is possible to run a segmentation algorithm on the run (tests were run in the CV module, showing it is viable and that it is not necessary to implement opticalflow to conserve computational power).

After the segmentation part, each object will have a mask and we can divide the LIDAR data regarding measurements in each visual element, in order to

- measure relative position of objects, in order to return a list of objects and positions to be updated on the HMI module
- measure the relative position on walls, ceiling and floor, in order to compute the location of the robot and send it to the HMI module

#### Deciding behaviour

##### Inferring position of objects

Once the real time collected data is processed into a list of objects and relative positions, it can be used by the HMI module.

The data regarding relative position of the robot can be used, given a priori knowledge of the map under which the robot moves, in order to infer a short list of points in which the robot may be at. These points may be interpreted as the intersection points of offsets of the walls. Distinguishing between the points is a matter of choosing which is closer to the previous position of the robot.

Once the HMI module has knowledge of the current position of the robot, it is possible to use the relative position of the objects to compute their absolute position. It is also possible to use RGB + LIDAR data to, either

- Build a 3d representation of the object on the run, with precise dimensions (useful only to stationary objects);
- Use the positions, crude measurements of the size of the object and create virtual cilinders for the robot to avoid; or
- Use the positions, crude measurements and image to use models such as [state of the art models for creating 3d objects with 2d images](https://wgsxm.github.io/projects/partcrafter).

##### Deciding which direction to move

Once we have a complete representation of the objects in the scene, we can decide for paths to be taken by the robot, with the requisites of (see more on the HMI module).

- Having a randomization agent to suggest points where the robot shoud move to, with a optional routine that prevents the robot to be to much time in one place (as we dont have anough patience to invoke the law o big numbers);
- Having some path finding algorithms to choose (based on trees, optimizing an optimal path, etc);
- Having a easy, smartphone inspired enviroment for a human user to suggest behaviours.

##### Deciding complex behaviour

Moving the robot around is the easy part. The challange that justifies the use of biped robots is to use complex behaviour in order to interact with the enviroment, such as looking around objects, looking under tables, getting chairs off the way, pushing buttons, etc. These behaviours shoud be able to be invoked by the human user, as well as independently devided by the robot.

Interacting with humans shoud also be viable, possibly integrating LLM's with write to speech capabilities, possibly handling verbal requests (hard coded or procedurally generated) to perform actions.

##### Human interfearence detector

It should be checked at all times if the robot is being handled by a human, as the use case scenario aimed upon is that of a patrol device.

Deviations of measurements of the IMU sensors from expected and data that deviates from the quadratic and trigonometric behaviour of free falling shoud be enough. Sideways movement should also be useful in this task.
