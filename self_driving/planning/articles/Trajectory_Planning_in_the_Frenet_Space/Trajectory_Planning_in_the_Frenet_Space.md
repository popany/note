# [Trajectory Planning in the Frenet Space](https://fjp.at/posts/optimal-frenet/)

- [Trajectory Planning in the Frenet Space](#trajectory-planning-in-the-frenet-space)
  - [Algorithm](#algorithm)
  - [Frenet Coordinates](#frenet-coordinates)
    - [Frenet Features](#frenet-features)
    - [Reference Path](#reference-path)
      - [Clothoid](#clothoid)
      - [Polyline](#polyline)
    - [TransformationPermalink](#transformationpermalink)

There are many ways to plan a trajectory for a robot. A trajectory can be seen as a set of time ordered state vectors $x$. The following algorithm introduces a way to plan trajectories to maneuver a mobile robot in a 2D plane. It is specifically useful for structured environments, like highways, where a rough path, referred to as reference, is available a priori.

Path planning in frenet coordinates:

Trajectory Planning using frenet coordinates.

![fig1](./optimal_frenet.gif)

## Algorithm

1. Determine the trajectory start state $[x_1,x_2,\theta,\kappa,v,a](0)$ The trajectory start state is obtained by evaluating the previously calculated trajectory at the prospective start state (low-level-stabilization). At system initialization and after reinitialization, the current vehicle position is used instead (high-level-stabilization).

2. Selection of the lateral mode Depending on the velocity $v$ the time based ($d(t)$) or running length / arc length based ($d(s)$) lateral planning mode is activated. By projecting the start state onto the reference curve the the longitudinal start position $s(0)$ is determined. The frenet state vector $[s,\dot{s},\ddot{s},d,d',d''](0)$ can be determined using the frenet transformation. For the time based lateral planning mode, $[\dot{d}, \ddot{d}](0)$ need to be calculated.

3. Generating the laterl and longitudinal trajectories Trajectories including their costs are generated for the lateral (mode dependent) as well as the longitudinal motion (velocity keeping, vehicle following / distance keeping) in the frenet space. In this stage, trajectories with high lateral accelerations with respect to the reference path can be neglected to improve the computational performance.

4. **Combining lateral and longitudinal trajectories** Summing the partial costs of lateral and longitduinal costs using $J(d(t),s(t)) = J_d(d(t)) + k_s \cdot J_s(s(t))$, for all active longidtuinal mode every longitudinal trajectory is combined with every lateral trajectory and transfromed back to world coordinates using the reference path. The trajectories are verified if they obey physical driving limits by subsequent point wise evaluation of curvature and acceleration. This leads to a set of potentially drivable maneuvers of a specific mode in world coordinates.

5. **Static and dynamic collision check** Every trajectory set is evaluated with increasing total costs if static and dynamic collisions are avoided. The trajectory with the lowest cost is then selected.

6. Longitudinal mode alternation Using the sign based (in the beginning) jerk $\dot{a}(0)$, the trajectory with the strongest decceleration or the trajectory which accelerates the least respectively is selected and passed to the controller.

## Frenet Coordinates

"Frenet Coordinates", are a way of representing position on a road in a more intuitive way than traditional $(x,y)$ Cartesian Coordinates.

With Frenet coordinates, we use the variables $s$ and $d$ to describe a vehicle's position on the road or a reference path. The $s$ coordinate represents distance along the road (also known as longitudinal displacement) and the $d$ coordinate represents side-to-side position on the road (relative to the reference path), and is also known as lateral displacement.

In the following sections the advantages and disadvantages of Frenet coordinates are compared to the Cartesian coordinates.

### Frenet Features

The image below depicts a curvy road with a Cartesian coordinate system laid on top of it, as well as a curved (continuously curved) reference path (for example the middle of the road).

Representation of a reference path (blue) in Cartesian coordinates $(x,d)$.

![fig2](./cart_refpath.svg)

The next image shows the same reference path together with its Frenet coordinates.

Representation of a reference path in Frenet coordinates $(s,d)$ on a road segment.

![fig3](./frenet_refpath.svg)

The $s$ coordinate represents the run length and starts with $s = 0$ at the beginning of the reference path. Lateral positions relative to the reference path are are represented with the $d$ coordinate. Positions on the reference path are represented with $d = 0$. $d$ is positive to the left of the reference path and negative on the right of it, although this depends on the convention used for the local reference frame.

The image above shows that curved reference paths (such as curvy roads) are represented as straight lines on the $s$ axis in Frenet coordinates. However, motions that do not follow the reference path exactly **result in non straight motions in Frenet coordinates**. Instead such motions result in an offset from the reference path and therefore the $s$ axis, which is described with the $d$ coordinate. The following image shows the two different representations (Cartesian vs Frenet).

Comparison of a planned trajectory in Cartesian and Frenet coordinates.

![fig4](./compare_cart_frenet.svg)

To use Frenet coordinates **it is required to have a continouosly smooth reference path**.

### Reference Path

Frenet coordinates provide a mathematically simpler representation of a reference path, because its run length is described with the $s# axis. This reference path provides a rough reference to follow an arbitrary but **curvature continuous course** of the road. To avoid collisions, the **planner must take care of other objects in the environment, either static or dynamic**. Such objects are usually not avoided by the reference path.

A reference path can be represented in two different forms although for all representations a run length information, which represents the $s$ axis, is required for the transformation.

- Polynome

- Spline (multiple polynomes)

- Clothoid (special polynome)

- Polyline (single points with run length information)

#### Clothoid

$x(l) = c0 + c1*l$

#### Polyline

### TransformationPermalink

The transformation from **local vehicle coordinates** to Frenet coordinates is based on the relations shown in the following image:

Transformation vehicle frame to Frenet frame.









TODO xxxxxx

