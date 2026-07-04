# MATLAB-Robotics

# Matlab - The Basics
Have you ever built a circuit, watched it do something weird, and thought "I bet there's a cleaner way to know why this is happening, instead of just poking at it with a multimeter and hoping"? That right there is basically the entire pitch for this post.

<img src =imgs/matlab_logo.png width = "50%">
If you've spent any time around circuit labs, embedded systems courses, or hardware prototyping benches, you've probably heard one name come up again and again: Matlab. It's got a reputation as the "engineer's calculator on steroids," and in various fields of engineering, it is used for a variety of operations and applications : from crunching raw numbers to simulating an entire physical system before a single wire gets soldered, the possibilities are endless.<br />
<br />

**So what exactly is MATLAB?**

At its core, it's a numerical computing environment and programming language built around matrices (the name literally stands for **Matrix Laboratory**). That might sound abstract, but here's the thing a: huge chunk of engineering secretly is matrices. Solving a circuit with multiple nodes, tracking how a signal evolves over time or performing calculations that would take hours by hand are only a small fraction of the tasks that MATLAB can perform.

## A Quick Look Around: The MATLAB Interface
Before we go any further, let's get oriented because opening MATLAB for the first time and staring at three panels you don't recognise is its own special kind of confusion. <br />
<img src=imgs/interface.png>
<br />
Here's what you're actually looking at:
* Current Folder (left , top) — this is your file browser. Whatever folder is open here is where MATLAB will look for your scripts and save your files. Keep this organised and you'll save yourself a lot of "where did that file go" moments later.
* Command Window (centre, right) — this is where MATLAB lives and breathes. You can type commands directly here and run them instantly, without writing a full script. Great for quick calculations, testing a single line of code, or just checking what a variable looks like. Think of it as your scratch pad.
* Workspace (bottom left) — every variable you create, whether by running a script or typing directly into the Command Window, shows up here. Name, type, size, value — all listed out, so you can see exactly what's in memory at any given moment. When something isn't behaving the way you expect, the Workspace is usually the first place to look.

Here are some resources that explain the basics of Matlab in a slightly more comprehensive manner:
* [Matlab Crash Course - By Younes Lab](https://youtu.be/sLxdNdC6Mds?si=20N6yzAYCiUGTa-O)
* [Complete Matlab Beginner Basics Course - By Phil Parisi](https://youtu.be/EtUCgn3T9eE?si=n-wQJrUJUJ3GnoKr)

But as you can probably tell, MATLAB by itself is mostly about writing and running code line by line. So what do we do when we want to simulate an entire system, one which evolves over time, reacts to inputs and responds like the real thing would? Simulating such a system through code would be an incredibly tedious task. Thankfully, MATLAB provides us with a solution in the form of **Simulink**.

## Simulink
<img src=imgs/simulink.png width="50%">
Simulink is MATLAB's graphical simulation environment. Instead of writing everything as text-based code, you build your system as a block diagram : drag in a signal source, a controller, a plant model, wire them together, hit run, and watch the signals evolve on a virtual scope. It's the same underlying math as a MATLAB script, just represented visually, which turns out to be incredibly useful when a system has a lot of moving, interacting parts.

Let's see how this actually works under the hood, because "drag blocks, wire them up" is doing a lot of hand-waving right now : 
Every Simulink system, no matter how complicated it eventually gets, boils down to just two things: **blocks and lines**.
Blocks do the work : each one performs some operation, whether that's generating a signal, doing math on it, or just displaying it. 
Lines are the wires : they carry signals from one block's output straight into another block's input. 
That's genuinely it. Everything else is just more blocks and more lines, arranged in increasingly elaborate ways.

Now, blocks can generally be divided into three distinct categories and once you can spot which is which, reading any Simulink diagram gets a lot easier:

* Source blocks : these are where signals are born. They don't take any input; they just sit there generating something for the rest of the system to chew on. A Sine Wave block, a Ramp block, a Step input, all of these are sources. Think of them as the starting point of the story.
* Processing blocks : this is the middle of the story, where stuff actually happens. These blocks take an input, do something to it (add, multiply, integrate, filter, whatever the block is built for), and pass an output downstream. Most of the "logic" of your system lives here.
* Sink blocks : and finally, the end of the line, literally. Sink blocks take an input but produce no output of their own; they exist purely to show you or stop something. The Scope block (your virtual oscilloscope) is the classic example, along with the Stop Simulation block, which does exactly what it sounds like.

Source feeds into processing, processing feeds into sink  and just like that, you've got a working simulation, built entirely out of arranging these three categories in whatever order your system actually needs.

📖 A full tutorial on how to use Simulink can be found here: <br />
[▶️Getting Started With Simulink - Mathworks](https://youtube.com/playlist?list=PL484BA2AD3AE4C2D0&si=ISXrRSDpdc_2aQg6)

## Simscape
Now, within Simulink, there's a more specialized layer called Simscape. Where regular Simulink blocks represent abstract signals and math operations, Simscape blocks represent actual physical components: resistors, capacitors, motors, gears, pipes, you name it. You're not writing the differential equations that describe how these things behave; Simscape already knows the physics. You just wire the components together the way you'd wire them on a breadboard (or a schematic), and the simulation figures out the rest. 

<img src="imgs/Simscape.png" width="50%"/>
For instance let’s look at this case: in Simulink, that's a block diagram doing the math behind springs and dampers; in Simscape, that's literally a Spring block and a Damper block, wired together the way you'd actually wire physical components on a bench. Same system, two completely different ways of representing it.

So naturally, the question becomes: what happens when you want both at once? Say you've built your physical spring-damper setup in Simscape, but you want to feed its output into a Simulink controller, or plot i.t on a regular Simulink scope. Can you just... wire them together?
WELL, not quite and here's why. Simulink blocks talk to each other using mathematical signals: plain numbers flowing along directional lines, one block's output becoming the next block's input. Simscape components, on the other hand, talk to each other through physical connections, think actual current flowing through a wire, or actual force transmitted through a mechanical joint, where the connection itself doesn't have a single "direction" the way a signal does. These two worlds speak fundamentally different languages, and you can't just slap a wire between them and expect MATLAB to figure out what you meant.

This is where converter blocks come in, they're the translators sitting at the border between these two worlds, and you'll need one any time a signal needs to cross from one side to the other:
* PS Converter (Simulink-to-Simscape) — takes a regular Simulink signal and converts it into a physical signal that Simscape components can actually use as an input.
<img src=imgs/ps.png width="25%">
- SP Converter (Simscape-to-Simulink) — does the reverse, taking a physical signal from a Simscape component and converting it back into a plain Simulink signal you can feed into a scope, a controller, or any other ordinary block.
<br />
<img src=imgs/sp.png width="25%">
<br />
Once you've got these two converters in your toolkit, mixing the two worlds stops being a problem. You can build the physical part of your system in Simscape, where it genuinely belongs, and still hook it straight into all the controllers, scopes, and logic you're used to building in plain Simulink.
<br />

📖 Here's another tutorial on how to use Simscape: <br />
[▶️Physical Modeling in Matlab - Mathworks](https://youtube.com/playlist?list=PLn8PRpmsu08qZutTT-7dRthkAnFuQjCOV&si=JY9XuIw65GtuIJ5F)

## Toolboxes
Okay, so at this point you've got a decent picture of how MATLAB, Simulink, and Simscape fit together : one's for writing code, one's for visual block-diagram simulation, and one's for modeling actual physical components. But there's still a piece of the puzzle we haven't really unpacked: what makes MATLAB capable of doing any of this specialized stuff in the first place? <br />
The answer is toolboxes and to be honest, this is probably the single most important concept to understand about how MATLAB actually works in practice.
Base MATLAB, on its own, gives you a solid general-purpose numerical computing environment: matrices, basic plotting, a programming language to tie it all together. Useful, but generic. Toolboxes are what take that generic foundation and bolt on deep, domain-specific functionality for whatever field you're working in without you having to implement any of it yourself.<br />
Toolboxes hand you pre-built functions, blocks, and components, all ready to drop into your project the moment you need them, no reinventing required.

---
So, with that out of the way, let's actually put this to use. We're going to walk through how to simulate a robot in MATLAB, piece by piece.
Now, since every robot eventually needs to be powered, sensed, and controlled before it's allowed to move an inch, that's exactly where we'll start: the electronics part.





# The Electronics Part: Simscape Electrical and Friends

Strip away the motors, the joints, the fancy kinematics and every robot is ultimately just a circuit that happens to be shaped like a robot. Power has to get from a battery to a motor. A sensor has to convert something physical (flex, pressure, light) into a voltage your microcontroller can actually read. None of the mechanical stuff works until the electrical stuff works first. So that's where we're starting.

## Simscape Electrical

This is the big one for electronics simulation in MATLAB. Remember what we said about Simscape knowing the physics so you don't have to? Simscape Electrical is that same idea, applied specifically to electrical and electronic systems.

It gives you a library of any and all electrical components that you can think of :resistors, capacitors, inductors, diodes, MOSFETs, op-amps, transformers, motor drives, batteries etc. which you can wire together exactly the way you'd wire them on a schematic. No writing out differential equations, no manually deriving how a MOSFET switches, the toolbox already has all of that baked in. You just place the components, connect them, and simulate. <br />
<img src=imgs/simscape_electrical.png width="50%">


What makes it genuinely useful for robotics specifically? A few things:

* **Motor and drive simulation** — Simscape Electrical has dedicated blocks for DC motors, BLDC motors, stepper motors, and the drive circuits that control them. You can model the full electrical behaviour of a motor: back-EMF, winding resistance, inductance and watch how it responds to a PWM signal before you've touched a motor driver IC. For anyone who's ever burned out an L298N because they didn't fully understand what was happening electrically, this is the part that would have saved you.

* **Power electronics** — H-bridges, buck converters, boost converters, PWM generators, all available as blocks. If your robot runs on a battery and needs regulated voltages at different levels (say, 12V for motors and 5V for logic), you can model the entire power chain and verify it before ordering a single component.

* **Sensor front-ends** — flex sensors, current sensors, voltage dividers, op-amp signal conditioning circuits, these are all just passive and active component arrangements, and Simscape Electrical handles all of them. You can model exactly what voltage range you'll get out of a sensor under different conditions, which saves a lot of "why is my ADC reading garbage" debugging later.

📖 *Go further with Simscape Electrical:*
- [▶️ What is Simscape Electrical – MathWorks YouTube](https://youtu.be/W6H71Fb0MTc?si=1PTjoHhZsaKPhzAE)
- [▶️ Introduction to Electrical System Modeling – MathWorks](https://youtu.be/AMnzljjkbB4?si=ckmZWc8bDDHrw59E)
- [📄 Simscape Electrical Documentation](https://www.mathworks.com/help/sps/)

---

#### Let's See It in Practice: A DC Motor Circuit

📖 *Some resources on motor modelling:*
- [▶️ DC Motor Control with Simulink – Donghwa Ryu](https://youtu.be/ArSDxzfLSxo?si=XKFLJxw-sSKdVt8c)
- [📄 DC Motor Model – MathWorks Documentation](https://www.mathworks.com/help/sps/ref/dcmotor.html)

---

## DAQ Toolbox — Bringing Real Hardware Into the Picture

Here's where simulation meets reality. You've modelled your motor circuit in Simscape Electrical but at some point, you need to actually run this on hardware and verify that the real world agrees with what your simulation predicted. That's where the **Data Acquisition Toolbox** comes in.

DAQ Toolbox lets MATLAB talk directly to physical hardware : DAQ boards, Arduinos, sensors and pull live measurements straight into your workspace. No manually exporting CSVs from an oscilloscope, no transcribing numbers off a screen. You plug in, run a few lines, and the data is in a MATLAB variable ready to be plotted or compared against your simulation output.

For a robotics project this matters enormously. You can log your motor's actual current draw during operation, compare it against what Simscape Electrical predicted, and immediately spot if something in your real circuit isn't matching the model, maybe a component value is off, or there's something in the physical system you haven't accounted for.

```matlab
% Connect to a DAQ device and capture live motor current
d = daq("ni");                         % initialise DAQ session
addinput(d, "Dev1", "ai0", "Voltage"); % current sensor output on channel 0

d.Rate = 5000;                         % 5000 samples/sec — fast enough for motor dynamics

disp('Capturing 3 seconds of motor current data...');
data = read(d, seconds(3));

% Plot and compare against simulation
plot(data.Time, data.Dev1_ai0);
xlabel('Time (s)');
ylabel('Measured Current (A)');
title('Real Motor Current vs Time');
grid on;
```



*🔧 Try it yourself — if you don't have a DAQ board, MATLAB's Arduino support package lets you do a version of this with a regular Arduino Uno over USB. Install it via the Add-On Explorer and try logging a potentiometer voltage first before moving to anything motor-related.*

📖 *Go further with DAQ Toolbox:*
- [▶️ What is DAQ Toolbox – MathWorks YouTube](https://youtu.be/oUQ4JtxZL8Q?si=r7XYnMQVJtvllqzi)
- [📄 DAQ Toolbox Documentation](https://www.mathworks.com/help/daq/)
- [📄 MATLAB Arduino Support Package](https://www.mathworks.com/hardware-support/arduino-matlab.html)

---

### How These Two Work Together

Here's the part worth pausing on, because it's pretty easy to miss when you're learning each toolbox separately.

Simscape Electrical and DAQ Toolbox aren't two separate tools you pick between, they're basically two halves of the same workflow:

- **Simscape Electrical** lets you model and simulate the circuit before anything exists physically : catch problems on screen before they become problems on a bench
- **DAQ Toolbox** lets you validate that simulation against real hardware once you've built it : confirm that the real world actually agrees with what your model predicted

Model first, measure after, compare both. That loop is how real electronics engineering actually works and having both layers inside the same MATLAB environment, sharing the same variables, the same plots, the same workspace is what makes it genuinely practical rather than just theoretically nice.



With the electronics side covered, it's time to move up a level — from the circuits that power the robot, to the mechanical body the robot actually moves with. That's where Simscape Multibody comes in, and that's exactly where we're headed next.





# NAVIGATION

**Robotics System Toolbox** handles the robot's body — kinematics, manipulators, mobile robot models. **Navigation Toolbox** handles the robot's brain: where it is, what its environment looks like, and how to move through that environment. In fact, Robotics System Toolbox leans on it directly — you can design customizable motion planners by leveraging Navigation Toolbox.

This section covers planning a path, modeling sensors, doing SLAM, and planning in 3D. .

## 1. Build a Map: `occupancyMap`

The most basic building block is the occupancy map — a grid where each cell holds a probability that it's occupied by an obstacle. Values close to 1 represent a high probability that the cell contains an obstacle, while values close to 0 represent a high probability that the cell is free.

```matlab
% Create an empty 10m x 10m map at 20 cells/meter
map = occupancyMap(10,10,20);

% Mark a wall and a block of obstacles using world coordinates
wallX = 5*ones(1,40);
wallY = linspace(1,8,40);
setOccupancy(map,[wallX' wallY'],1)

[blockX,blockY] = meshgrid(2:0.2:3,2:0.2:3);
setOccupancy(map,[blockX(:) blockY(:)],1)

% Inflate obstacles to add a safety buffer around them (e.g. robot radius)
inflate(map,0.3)

show(map)
```

<img src="imgs/Occupancymap.png" alt="Occupancy Map" width="50%"/>

`setOccupancy` marks specific world coordinates as occupied (1) or free (0), and `inflate` grows every occupied cell outward by a given radius. This is how we bake in clearance for the robot's physical size before planning a path through the map. 

## 2. Plan a Path: `plannerRRT`

Once you have a map, the toolbox provides customizable search and sampling-based path planners, as well as metrics for validating and comparing paths. The classic example is RRT (Rapidly-exploring Random Tree). The workflow is always: define a state space → define a state validator tied to your map → create the planner → call `plan`.

```matlab
% Load an example map
load exampleMaps
map = occupancyMap(simpleMap,10);

% Define an SE(2) state space [x y theta] and tie it to the map
ss = stateSpaceSE2;
ss.StateBounds = [map.XWorldLimits; map.YWorldLimits; [-pi pi]];

% Validate states against the occupancy map
sv = validatorOccupancyMap(ss,Map=map);
sv.ValidationDistance = 0.01;

% Create the RRT planner
planner = plannerRRT(ss,sv,MaxConnectionDistance=0.3);

start = [0.5 0.5 0];
goal  = [2.5 0.2 0];

[pathObj,solnInfo] = plan(planner,start,goal);

show(map)
hold on
plot(solnInfo.TreeData(:,1),solnInfo.TreeData(:,2),'.-')   % tree expansion
plot(pathObj.States(:,1),pathObj.States(:,2),'r-','LineWidth',2)  % final path
```

<img src="imgs/RRT.png" width="50%"/>

Swap `plannerRRT` for `plannerRRTStar` if you want the planner to keep optimizing the path after first reaching the goal — useful when path *quality*, not just feasibility, matters.

```matlab
% Same map, state space, and validator as above

starPlanner = plannerRRTStar(ss,sv, ...
    MaxConnectionDistance=0.3, ...
    MaxIterations=2000, ...            % don't stop after a quick first hit
    ContinueAfterGoalReached=true, ... % keep refining once the goal is found
    MaxNumTreeNodes=2000);

[optimalPath,optimalInfo] = plan(starPlanner,start,goal);

show(map)
hold on
plot(optimalInfo.TreeData(:,1),optimalInfo.TreeData(:,2),'.-')
plot(optimalPath.States(:,1),optimalPath.States(:,2),'g-','LineWidth',2)
```
<img src="imgs/rrts.png" width="50%"/>

The first `plannerRRT` snippet stops the moment it finds *any* valid path. This `plannerRRTStar` version keeps sampling and rewiring the tree for up to 2,000 iterations, even after the goal is reached, so the final path tends to be shorter and smoother instead of just "first thing that worked."

## 3. Simulate a Sensor: `imuSensor`

Real robots don't get clean position and orientation for free — they infer it from noisy sensors. The toolbox lets you simulate that: you can model and tune parameters for IMU sensors, including accelerometer, gyroscope, and magnetometer characteristics, and configure noise profiles, biases, and drift to match real hardware.

```matlab
Fs = 100;  % sample rate, Hz

% Default IMU model: ideal accelerometer + gyroscope
IMU = imuSensor('accel-gyro','SampleRate',Fs);

trueAcceleration    = [1 0 0];
trueAngularVelocity = [1 0 0];

[accelReadings,gyroReadings] = IMU(trueAcceleration,trueAngularVelocity);

% Now make it realistic: add a constant gyroscope bias
IMU.Gyroscope.ConstantBias = [0.4 0 0];   % rad/s
[~,biasedGyroReadings] = IMU(trueAcceleration,trueAngularVelocity);
```

This is the same pattern used to test localization algorithms before they ever touch real hardware — generate synthetic noisy data, then run your fusion filter against it.

IMU alone only gives you relative motion, which drifts over time. Pair it with `gpsSensor` to model the other half of a typical GPS/INS setup — an absolute, if noisier, position fix:

```matlab
fs = 1;                          % 1 Hz update rate
refLoc = [42.2825 -71.343 53.0352];   % reference lat/lon/alt

gps = gpsSensor('SampleRate',fs,'ReferenceLocation',refLoc);

truePosition = [1 0 0];   % local NED position
trueVelocity = [1 0 0];

[lla,velocity,groundspeed,course] = gps(truePosition,trueVelocity);
```

Fusing the two — IMU for short-term smoothness, GPS for long-term correction — is exactly the kind of inertial navigation system the toolbox is built to simulate and tune before deployment.

## 4. SLAM: `lidarSLAM`

The toolbox provides algorithms and analysis tools for simultaneous localization and mapping (SLAM), letting a robot build a map of an unknown environment while simultaneously tracking its own position in it. `lidarSLAM` takes lidar scans, attaches them to a node in a pose graph, and automatically detects loop closures to correct drift.

```matlab
maxLidarRange = 8;     % meters
mapResolution = 20;    % cells per meter

slamAlg = lidarSLAM(mapResolution,maxLidarRange);
slamAlg.LoopClosureThreshold = 360;
slamAlg.LoopClosureSearchRadius = 8;

% In a real application "scans" comes from your robot's lidar driver.
% Here we build a small cell array of lidarScan objects from raw
% ranges/angles, the same shape your sensor data would arrive in:
angles = deg2rad(-90:1:90);   % a 180-degree sweep
numScans = 5;
scans = cell(numScans,1);
for i = 1:numScans
    ranges = 5 + 0.1*i + 0.05*randn(size(angles));  % synthetic noisy ranges
    scans{i} = lidarScan(ranges,angles);
end

% Feed each scan into the SLAM object
for i = 1:numel(scans)
    addScan(slamAlg,scans{i});
end

show(slamAlg)   % view the built map + pose graph

% Convert the SLAM result into a standard occupancy map
[scansSLAM,poses] = scansAndPoses(slamAlg);
occMap = buildMap(scansSLAM,poses,mapResolution,maxLidarRange);
show(occMap)
```

`lidarScan(ranges,angles)` is what turns raw sensor readings into the object `addScan` expects. On real hardware, `ranges` and `angles` come straight from your lidar driver (or a ROS `LaserScan` message); the loop above just stands in for that data source so the snippet runs end-to-end.

## 5. Going 3D: `stateSpaceSE3`

Everything above used `stateSpaceSE2` — fine for ground robots, but drones and manipulators move through 3D space. Swap in `stateSpaceSE3` and `validatorOccupancyMap3D` and the same RRT workflow plans through a volume instead of a plane. The state vector grows to `[x y z qw qx qy qz]` — position plus a quaternion for orientation:

```matlab
% omap is a pre-built occupancyMap3D of a city block
ss = stateSpaceSE3([-20 220; -20 220; -10 100; ...
                     inf inf; inf inf; inf inf; inf inf]);

sv = validatorOccupancyMap3D(ss,Map=omap,ValidationDistance=0.1);

planner = plannerRRT(ss,sv, ...
    MaxConnectionDistance=50, ...
    MaxIterations=1000, ...
    GoalReachedFcn=@(~,s,g)(norm(s(1:3)-g(1:3))<1), ...
    GoalBias=0.1);

start = [40 180 25 0.7 0.2 0 0.1];
goal  = [150 33 35 0.3 0 0.1 0.6];

[pathObj,solnInfo] = plan(planner,start,goal);
```

Same planner object, same `plan` call — just a different state space and validator underneath. That consistency is the toolbox's biggest practical strength: learn the RRT pattern once on a 2D map, and it transfers almost unchanged to 3D, to Dubins/Reeds-Shepp vehicle models, or to a custom state space you define yourself.

## A Common Pitfall

If a planner silently returns a useless or invalid path, check `ss.StateBounds` first. It's easy to create the state space and the map separately and forget to sync them — `ss.StateBounds = [map.XWorldLimits; map.YWorldLimits; [-pi pi]];` is doing real work in every 2D example above, not just boilerplate. Skip it and the planner samples states outside (or only partially inside) the area your map actually covers, which produces paths that look fine in isolation but don't match the environment you intended to plan against.

## Where This Fits

| Toolbox | Job |
|---|---|
| **Robotics System Toolbox** | Robot body: kinematics, dynamics, manipulators |
| **Navigation Toolbox** | Robot brain: maps, SLAM, localization, path planning |

Worth a footnote: MathWorks' Robotics System Toolbox is a different lineage from the open-source **Robotics Toolbox for MATLAB** by Peter Corke — its functionality significantly overlaps that of the Robotics Toolbox for MATLAB but the programming model is quite different, so don't assume code from one applies to the other.

## Wrapping Up

Five building blocks: `occupancyMap` for the world, `plannerRRT` for getting through it, `imuSensor`/`gpsSensor` for realistic sensor input, `lidarSLAM` for mapping the unknown, and `stateSpaceSE3` for taking the same planning pattern into 3D. Everything else in the toolbox — RRT*, Bi-RRT, GPS/INS fusion filters, vehicle-specific state spaces — builds on these same patterns. Once you're comfortable swapping objects in and out of this pipeline, the rest of the documentation reads less like a reference manual and more like a menu.

---
*All code verified against MathWorks Navigation Toolbox documentation (occupancyMap, setOccupancy, inflate, plannerRRT, plannerRRTStar, imuSensor, gpsSensor, lidarScan, lidarSLAM, stateSpaceSE3, validatorOccupancyMap3D reference pages) and product overview pages.*
