# Technical Documentation

This folder contains technical documentation for the LLM-enabled humanoid robot project.

The files in this folder explain the system architecture, LLM tool-calling interface, Planner/Controller validation logic, vision system, motion control approach, and project limitations.

The full implementation code is not included in this public repository. These documents are provided to describe the engineering design and system behaviour for portfolio, academic, and recruitment purposes.

---

## Files

### `system_overview.md`

Provides a high-level explanation of the complete humanoid robot pipeline, including perception, LLM reasoning, validation, motion control, and robot state updates.

### `llm_tool_calling.md`

Explains how the custom OAX-1B-Humanoid model converts natural language commands into structured JSON tool calls.

### `planner_controller.md`

Describes the validation layer between the LLM and the physical robot. This includes action approval, repair, rejection, clarification, and unsafe execution prevention.

### `vision_system.md`

Explains the camera-based perception pipeline, object detection outputs, perception state, and how visual context is passed to the reasoning layer.

### `kinematics_motion_control.md`

Summarises the IK/FK-supported motion planning approach, spatial mapping, target estimation, and low-level execution flow.

### `limitations_future_work.md`

Lists the current limitations of the prototype and future improvements such as ROS2 integration, simulation, better grasping, wrist-camera alignment, and closed-loop visual servoing.

---

## Documentation Purpose

The purpose of these documents is to explain the system without exposing the full implementation code.

The documentation focuses on:

```text
User Command
     |
     v
LLM Tool Call
     |
     v
Planner / Controller Validation
     |
     v
Robot Motion Planning
     |
     v
Physical Execution
```

The LLM does not directly control the robot motors. It proposes structured actions, while deterministic validation and control layers decide whether those actions are valid, safe, and executable.
