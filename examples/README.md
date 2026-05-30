# Example Task Flows

This document shows example task flows for the LLM-enabled humanoid robot system.

The examples are simplified and intended to demonstrate how natural language commands are converted into structured tool calls and validated before execution.

---

## Example 1: What Do You See?

### User Command

```text
What do you see?
```

### Expected Tool Call

```json
{
  "tool": "get_visible_objects",
  "arguments": {}
}
```

### System Flow

```text
1. Capture camera frame.
2. Run object detection.
3. Update visible object list.
4. Return detected objects to the user.
```

### Example Response

```text
I can see a bottle, a cup, and a laptop.
```

---

## Example 2: Find the Bottle

### User Command

```text
Find the bottle.
```

### Expected Tool Call

```json
{
  "tool": "search_object",
  "arguments": {
    "target": "bottle"
  }
}
```

### System Flow

```text
1. Check the current visible object list.
2. If the bottle is visible, centre or approach the target.
3. If the bottle is not visible, activate search behaviour.
4. Update the robot state after the search.
```

---

## Example 3: Find the Bottle and Pick It Up

### User Command

```text
Find the bottle and pick it up.
```

### Expected Action Sequence

```json
[
  {
    "tool": "get_visible_objects",
    "arguments": {}
  },
  {
    "tool": "search_object",
    "arguments": {
      "target": "bottle"
    }
  },
  {
    "tool": "pick_object",
    "arguments": {
      "target": "bottle"
    }
  },
  {
    "tool": "get_robot_status",
    "arguments": {}
  }
]
```

### System Flow

```text
1. Detect visible objects.
2. Search for the target object.
3. Estimate the target position.
4. Validate the pick action.
5. Move the arm towards the object.
6. Attempt grasping.
7. Update the robot state.
```

---

## Example 4: Invalid Action Repair

### Raw LLM Output

```json
{
  "tool": "pick_object",
  "arguments": {
    "target": "bottle"
  }
}
```

### Current Robot State

```json
{
  "visible_objects": [],
  "holding_object": false,
  "current_action": "idle"
}
```

### Controller Decision

```json
{
  "decision": "repair",
  "reason": "Target object is not visible. Search is required before pick.",
  "repaired_action": {
    "tool": "search_object",
    "arguments": {
      "target": "bottle"
    }
  }
}
```

### Explanation

The Controller prevents direct execution because the target object is not currently visible. Instead of allowing an unsafe or impossible pick attempt, it repairs the action by requesting a search step first.
