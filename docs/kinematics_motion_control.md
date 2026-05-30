# Kinematics and Motion Control

This document summarises the kinematics and motion control approach used in the LLM-enabled humanoid robot project.

The robot uses IK/FK-supported motion planning to convert target information from the vision system into robot arm movement commands.

---

## Purpose

The motion system connects validated robot actions to physical execution.

It is responsible for:

- predefined robot poses,
- arm movement,
- object approach,
- gripper opening and closing,
- safe home position,
- serial command generation,
- low-level controller communication.

---

## Motion Pipeline

```text
Validated Tool Call
        |
        v
Target Object Information
        |
        v
Spatial Mapping
        |
        v
IK Target Estimation
        |
        v
Joint-Level Motion Command
        |
        v
Serial Communication
        |
        v
Arduino / Actuators
```

---

## Spatial Mapping

The system estimates target coordinates using:

- object pixel position,
- depth information,
- camera/head pose,
- robot geometry,
- approximate workspace constraints.

The goal is to convert visual object information into an approximate IK target for the robotic arm.

---

## IK/FK Role

Inverse kinematics is used to estimate joint positions for reaching a target.

Forward kinematics is used to check the resulting pose and estimate whether the target can be reached within the robot workspace.

---

## Execution

After validation and motion planning, the system sends movement commands to the low-level controller.

The physical execution layer controls:

- shoulder movement,
- elbow movement,
- wrist/end-effector alignment,
- gripper open/close actions,
- safe return-to-home behaviour.

---

## Limitations

The current motion system is limited by:

- fixed-base geometry,
- 5-DOF arm structure,
- limited approach angle,
- mechanical tolerance,
- gripper alignment sensitivity,
- object height variation,
- lack of force feedback,
- limited closed-loop visual servoing during grasping.

---

## Summary

The motion control system demonstrates a connection between perception, target estimation, IK/FK-supported planning, and physical movement.

The main future improvement area is reliable physical grasping through better calibration, improved gripper design, and closed-loop visual servoing.
