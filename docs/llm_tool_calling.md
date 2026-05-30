# LLM Tool Calling

This document explains how the LLM-enabled humanoid robot system uses structured JSON tool calling.

The custom OAX-1B-Humanoid model is designed to convert natural language user commands into robot-specific tool calls.

---

## Purpose

Natural language commands are flexible, but robot actions need to be structured, limited, and validated.

Instead of allowing the LLM to output free-form movement commands, the system requires the model to produce JSON-style tool calls.

Example user command:

```text
Find the bottle.
```

Expected LLM output:

```json
{
  "tool": "search_object",
  "arguments": {
    "target": "bottle"
  }
}
```

---

## Supported Tools

The system supports tool calls such as:

```text
get_visible_objects
search_object
track_object
pick_object
place_object
handover_object
go_home
stop_action
get_robot_status
```

---

## Tool Call Format

Each tool call follows this structure:

```json
{
  "tool": "tool_name",
  "arguments": {
    "key": "value"
  }
}
```

The `tool` field specifies the robot action.

The `arguments` field contains the required parameters for that action.

---

## Example Outputs

### Object Perception

```text
User: What do you see?
```

```json
{
  "tool": "get_visible_objects",
  "arguments": {}
}
```

---

### Object Search

```text
User: Find the bottle.
```

```json
{
  "tool": "search_object",
  "arguments": {
    "target": "bottle"
  }
}
```

---

### Object Tracking

```text
User: Track the bottle.
```

```json
{
  "tool": "track_object",
  "arguments": {
    "target": "bottle"
  }
}
```

---

### Object Pick

```text
User: Pick up the bottle.
```

```json
{
  "tool": "pick_object",
  "arguments": {
    "target": "bottle"
  }
}
```

---

### Stop Action

```text
User: Stop.
```

```json
{
  "tool": "stop_action",
  "arguments": {}
}
```

---

## Why JSON Tool Calls?

Structured tool calls make the robot system:

- easier to parse,
- easier to validate,
- easier to repair,
- easier to debug,
- safer for physical execution.

Raw language output is not suitable for direct motor control because it can be ambiguous or unsafe.

---

## Relationship with Planner and Controller

The LLM output is not executed immediately.

Instead, it is passed to the Planner and Controller:

```text
LLM Output
    |
    v
Planner
    |
    v
Controller
    |
    v
Validated Execution
```

The Controller checks whether the action is safe, valid, and executable before allowing the robot to move.

---

## Example Invalid Case

Raw LLM output:

```json
{
  "tool": "pick_object",
  "arguments": {
    "target": "bottle"
  }
}
```

If the bottle is not visible, this action should not be executed immediately.

Possible Controller repair:

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

---

## Summary

The LLM tool-calling interface is the bridge between natural language and robot behaviour.

The LLM proposes actions, but the Controller decides whether they can be safely executed.
