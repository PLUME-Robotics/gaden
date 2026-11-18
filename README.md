# GADEN

## A 3D Gas Dispersion Simulator for ROS

GADEN is a simulation framework designed for mobile robotics systems and gas sensing algorithms, also known as Mobile Robotics Olfaction (MRO). The framework is rooted in the principles of computational fluid dynamics and filament dispersion theory, modeling wind flow and gas dispersion in 3D scenarios and accounting for walls, furniture, and other objects that may have a significative impact on the gas dispersion.

Moreover, it integrates the simulation of different environmental sensors, such as metal oxide gas sensors, photo-ionization detectors, T-DLAS or anemometers, as well as it is fully integrated with ROS and the navigation stack, making testing and validation much easier.

If you do not want to use ROS, gaden is available as a [standalone library](https://github.com/MAPIRlab/gaden_core) and a [graphical desktop application](https://github.com/MAPIRlab/gaden_gui).

## Video showcase
[![Video showcase](https://img.youtube.com/vi/2i3_pyV-MYU/hqdefault.jpg)](https://youtu.be/2i3_pyV-MYU)

You can cite Gaden in your work using the following .bib:

```
@ARTICLE{gaden-rt-2025,
    author = {Ojeda, Pepe and Monroy, Javier and Gonzalez-Jimenez, Javier},
     title = {Gaden-RT: A Real Time and Interactive Gas Dispersion Simulator for Mobile Robotics},
   journal = {SoftwareX},
    volume = {32},
      year = {2025},
      issn = {2352-7110},
       url = {https://www.sciencedirect.com/science/article/pii/S2352711025003541},
       doi = {10.1016/j.softx.2025.102388}
}

@article{monroyGADEN3DGas2017,
     title = {{{GADEN}}: {{A 3D}} Gas Dispersion Simulator for Mobile Robot Olfaction in Realistic Environments},
    author = {Monroy, Javier and {Hernandez-Bennetts}, Victor and Fan, Han and Lilienthal, Achim and {Gonzalez-Jimenez}, Javier},
      year = {2017},
   journal = {Sensors (Switzerland)},
    volume = {17},
    number = {7},
     pages = {1--16},
       doi = {10.3390/s17071479},
}
```

## Installation
Move to your colcon workspace and run

`git clone --recurse-submodules git@github.com:MAPIRlab/gaden.git src/gaden`

It is important that you include the `--recurse-submodules` option, as the gaden repository includes submodules. if you already cloned in a non-recursive manner you can fix the problem by running

`git submodule update --init --recursive`

#### Dependencies
Although GADEN is a self-contained pkg, the also included "simulated_sensor_pkgs" (anemometer and gas sensors) depends on an external pkg defining some "olfaction" related msgs. This pkg is available in a different repository [olfaction_msgs](https://github.com/MAPIRlab/olfaction_msgs)

## Usage
See the [tutorial](GADEN_tutorial.md) for instructions on how to set up a simulation of your own or use the included [test environments](test_env).

Users who want to test their algorithms in complex environments but do not need to simulate any specific scenario can find an extensive repository of existing simulations in the [VGR dataset](https://mapir.isa.uma.es/mapirwebsite/?p=1708), which features simulations in 3D models of real houses, along with the configuration files and intermediate data.
---

PLUME Robotics Extension

This repository is the PLUME Robotics fork of [MAPIRlab GADEN](https://github.com/MAPIRlab/gaden).

The PLUME development branch is:

```text
plume/pipe-inspection
```

The fork extends the original GADEN ROS 2 simulator to support the PLUME pipe-inspection scenario and independently controlled gas sources.

## Pipe Inspection Scenario

The PLUME scenario is located at:

```text
test_env/scenarios/pipe_inspection/
```

It contains the CAD models, environment configuration, scene configuration, wind data and simulation configuration required for the pipe-inspection environment.

The simulation defines two point gas sources:

| Source | Position (x, y, z) | Gas type |
|---|---|---|
| Left | (-1.25, 0.0, 0.97) | 0 |
| Right | (1.25, 0.0, 0.97) | 0 |

The simulation configuration is located at:

```text
test_env/scenarios/pipe_inspection/environment_configurations/config1/simulations/sim1/sim.yaml
```

## Multi-Source Control

The PLUME fork extends the filament simulator with two ROS 2 Boolean subscriptions:

| Topic | Function |
|---|---|
| `/gaden/source_left_enabled` | Controls the left gas source |
| `/gaden/source_right_enabled` | Controls the right gas source |

The messages use:

```text
std_msgs/msg/Bool
```

The subscriptions call the corresponding source-enable functionality in GADEN Core:

- Source index `0`: left source.
- Source index `1`: right source.

This allows the gas sources to be controlled independently while the simulation is running.

The simulator also publishes a visual marker for each source, using unique marker IDs.

## Simulation Rate

The PLUME simulation launch file sets:

```python
limitRate = True
runRate = 10.0
```

This limits the simulation execution rate to 10 Hz.

## GADEN Core Dependency

This fork uses the PLUME Robotics GADEN Core fork rather than the original upstream Core repository.

The Core submodule uses the PLUME multi-source implementation, introduced in commit eb8787d.

```text
Repository: https://github.com/PLUME-Robotics/gaden_core
Branch:     plume/multi-source
Implementation commit: eb8787d
```

The Core extension provides the multi-source functionality required by the PLUME simulator.

## Running the Pipe Inspection Scenario

Build the GADEN packages in a ROS 2 Jazzy workspace, then source the workspace.

Preprocess the scenario first:

```bash
ros2 launch test_env gaden_preproc_launch.py \
    scenario:=pipe_inspection \
    configuration:=config1
```

After preprocessing completes, start the simulation:

```bash
ros2 launch test_env gaden_sim_launch.py \
    scenario:=pipe_inspection \
    configuration:=config1 \
    simulation:=sim1
```

For the complete project setup, including Gazebo, the ROS-Gazebo bridge and the valve controller, refer to the main [PLUME Simulation repository](https://github.com/PLUME-Robotics/Simulation).