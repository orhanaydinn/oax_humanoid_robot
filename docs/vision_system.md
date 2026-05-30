# Vision System

This document explains the vision system used in the LLM-enabled humanoid robot project.

The vision system provides object-level context for the robot and helps the LLM reason about the environment.

---

## Purpose

The vision system allows the robot to observe its surroundings and detect objects.

It is used for:

- detecting visible objects,
- identifying target objects,
- supporting search behaviour,
- estimating approximate object position,
- updating robot state,
- providing context to the LLM.

---

## Vision Pipeline

```text
Camera Input
    |
    v
Object Detection
    |
    v
Depth / Spatial Information
    |
    v
Perception State Update
    |
    v
LLM Context Input
```

---

## Example Perception Output

```json
{
  "visible_objects": ["bottle", "cup", "laptop"],
  "target": "bottle",
  "status": "visible"
}
```

A more detailed perception state may include confidence and approximate position:

```json
{
  "visible_objects": [
    {
      "label": "bottle",
      "confidence": 0.91,
      "position": {
        "image_center_x": 342,
        "image_center_y": 218,
        "depth_mm": 720
      }
    }
  ],
  "target_object": "bottle",
  "target_visible": true
}
```

---

## Role in LLM Reasoning

The LLM receives not only the user command, but also the perception context.

Example context:

```json
{
  "user_command": "Find the bottle.",
  "visible_objects": ["cup", "bottle", "laptop"],
  "robot_state": {
    "current_action": "idle",
    "holding_object": false
  }
}
```

This allows the LLM to generate a more context-aware tool call.

---

## Role in Controller Validation

The Controller uses perception data to check whether an action is valid.

For example, if the LLM generates:

```json
{
  "tool": "pick_object",
  "arguments": {
    "target": "bottle"
  }
}
```

but the bottle is not visible, the Controller can prevent the pick attempt and request a search action first.

---

## Vision System Evaluation

The Vision System was evaluated using object detection, target detection, head-movement search, and object centring.

| Test Type | Success Rate |
|---|---:|
| Visible object detection | 90% |
| Target object detection | 95% |
| Object search with head movement | 75% |
| Object centring | 85% |

The Vision System provided usable object-level context in controlled indoor conditions.

---

## Limitations

Vision performance can be affected by:

- lighting conditions,
- object position,
- camera angle,
- partial occlusion,
- detection confidence variation,
- depth estimation noise,
- head movement vibration.

---

## Summary

The vision system is a key part of the robot pipeline because it connects the physical environment to the LLM reasoning and Controller validation layers.
