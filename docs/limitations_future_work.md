# Limitations and Future Work

This document summarises the current limitations of the LLM-enabled humanoid robot prototype and planned future improvements.

---

## Current Limitations

The current system is a functional prototype, but several limitations remain.

### Mechanical Limitations

- fixed-base structure,
- limited 5-DOF arm flexibility,
- limited approach angles,
- mechanical tolerance from 3D-printed parts,
- backlash and small alignment errors,
- object height variation,
- gripper alignment sensitivity.

### Manipulation Limitations

- limited physical pick reliability,
- unstable grasping in some attempts,
- no force/torque sensing in the gripper,
- no robust grasp confirmation sensor,
- limited wrist-camera alignment,
- limited closed-loop visual servoing during grasp execution.

### Perception Limitations

- lighting sensitivity,
- camera angle dependency,
- partial occlusion issues,
- depth estimation noise,
- confidence variation during head movement.

### LLM and System Limitations

- LLM latency during real-time interaction,
- raw LLM outputs not reliable enough for direct execution,
- limited command benchmark size,
- no ROS2 implementation yet,
- no full simulation environment yet.

---

## Future Work

Planned future improvements include:

### Robotics and Control

- ROS2-based modular implementation,
- URDF/Gazebo or PyBullet simulation,
- improved wrist-camera alignment,
- better camera-to-robot calibration,
- improved gripper design,
- higher-DOF arm design,
- closed-loop visual servoing,
- more robust grasp detection.

### AI and LLM

- expanded robot-specific tool-use dataset,
- improved safety/refusal examples,
- better clarification handling,
- latency optimisation,
- local/cloud hybrid inference optimisation.

### Evaluation

- larger command-level benchmark dataset,
- automatic logging for all task attempts,
- ROS2-based logging and replay,
- physical pick success improvement tests,
- comparison between raw LLM output and validated execution,
- object detection evaluation under different lighting and object positions,
- simulation-based repeatable evaluation.

---

## Main Improvement Priority

The main improvement priority is physical manipulation reliability.

The perception, LLM tool-calling, and Planner/Controller validation layers showed stronger performance than the physical grasp execution stage.

Future work should therefore focus on:

```text
mechanical improvement
     +
better calibration
     +
closed-loop visual servoing
     +
improved gripper feedback
```

---

## Summary

The project demonstrates a working LLM-enabled perception-reasoning-validation-execution pipeline for a humanoid robot prototype.

The main future challenge is improving the reliability of real-world physical manipulation.
