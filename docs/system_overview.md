# System Overview

This document provides a high-level overview of the LLM-enabled humanoid robot system.

The project integrates a physical humanoid upper-body prototype with computer vision, natural language interaction, LLM-based reasoning, structured JSON tool calling, Planner/Controller validation, and low-level motion control.

---

## Main Idea

The robot is designed to interpret natural language commands and convert them into safe, validated robot behaviours.

Example command:

```text
Find the bottle and pick it up.
```

The system does not allow the LLM to directly control motors. Instead, the LLM produces structured tool calls, and the Planner/Controller layers validate those actions before execution.

---

## Pipeline

```text
User Command / Voice Input
        |
        v
Speech-to-Text / Text Input
        |
        v
Vision System + Robot State
        |
        v
LLM Reasoning Layer
        |
        v
Structured JSON Tool Call
        |
        v
Planner Module
        |
        v
Controller Validation
        |
        v
Motion Control Layer
        |
        v
Arduino / Robot Actuators
        |
        v
Environment Feedback / State Update
```

---

## Main System Layers

| Layer | Role |
|---|---|
| Input Layer | Receives voice or text commands from the user. |
| Perception Layer | Detects objects and provides visual context. |
| State/Memory Layer | Stores robot state, visible objects, target object, and task progress. |
| LLM Reasoning Layer | Converts user intent and context into structured JSON tool calls. |
| Planner Layer | Converts raw LLM output into a structured task representation. |
| Controller Layer | Validates, repairs, rejects, or approves proposed actions. |
| Motion Control Layer | Converts validated actions into robot movement commands. |
| Execution Layer | Sends commands to the low-level controller and robot actuators. |

---

## Why This Architecture?

A language model is flexible but not fully reliable for direct physical control. It may generate incomplete arguments, unsupported actions, ambiguous references, or unsafe instructions.

For this reason, the system separates:

```text
High-level reasoning  -> LLM
Validation            -> Planner / Controller
Physical execution    -> deterministic control layer
```

This makes the robot pipeline more interpretable, safer, and easier to debug.

---

## Example Flow

```text
User: Find the bottle.
```

1. The vision system checks visible objects.
2. The robot state is updated.
3. The LLM receives the command and context.
4. The LLM generates a JSON tool call.
5. The Planner converts it into a task.
6. The Controller validates the task.
7. The motion layer executes the approved action.
8. The robot state is updated after execution.

Example tool call:

```json
{
  "tool": "search_object",
  "arguments": {
    "target": "bottle"
  }
}
```

---

## Key Design Principle

The LLM is used as a high-level reasoning module, not as a direct motor controller.

Physical actions are only executed after deterministic validation.
