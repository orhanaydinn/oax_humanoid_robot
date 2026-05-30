# Planner and Controller

This document explains the Planner and Controller validation layers used in the LLM-enabled humanoid robot system.

These layers sit between the LLM and the physical robot execution system.

---

## Purpose

The Planner and Controller prevent raw LLM outputs from directly controlling the robot.

This is important because LLM outputs can be:

- incomplete,
- ambiguous,
- unsupported,
- unsafe,
- inconsistent with robot state,
- impossible to execute physically.

The validation layers reduce the risk of unsafe robot behaviour.

---

## Planner Role

The Planner converts the raw LLM output into a structured task representation.

Example raw LLM output:

```json
{
  "tool": "search_object",
  "arguments": {
    "target": "bottle"
  }
}
```

The Planner checks whether the output can be converted into a known task.

It may extract:

- tool name,
- target object,
- target location,
- task type,
- required preconditions,
- expected execution step.

---

## Controller Role

The Controller decides whether a planned action should be:

```text
approved
repaired
rejected
clarified
stopped
```

The Controller checks:

- whether the tool exists,
- whether required arguments are present,
- whether the target object is visible,
- whether the robot is already holding an object,
- whether the requested action is safe,
- whether the robot state allows the action,
- whether execution should continue, retry, or stop.

---

## Validation Examples

### Valid Action

```json
{
  "tool": "search_object",
  "arguments": {
    "target": "bottle"
  }
}
```

Possible Controller decision:

```json
{
  "decision": "approve",
  "reason": "The target object is valid and search can be executed."
}
```

---

### Missing Argument

```json
{
  "tool": "pick_object",
  "arguments": {}
}
```

Possible Controller decision:

```json
{
  "decision": "reject",
  "reason": "pick_object requires a target object."
}
```

---

### Target Not Visible

```json
{
  "tool": "pick_object",
  "arguments": {
    "target": "bottle"
  }
}
```

If the bottle is not visible:

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

### Ambiguous Command

```text
Pick that up.
```

Possible Controller decision:

```json
{
  "decision": "clarify",
  "reason": "The target object is ambiguous.",
  "message": "Which object should I pick up?"
}
```

---

## Why This Layer Matters

The raw LLM achieved valid JSON formatting, but raw LLM outputs are not reliable enough for direct robot execution.

The Planner/Controller layer improves reliability by:

- preventing unsafe execution,
- repairing incomplete actions,
- rejecting impossible actions,
- asking clarification when needed,
- checking robot state before motion.

---

## Summary

The Planner and Controller are the safety and validation bridge between language reasoning and physical robot execution.

The LLM proposes actions.

The Controller decides whether the robot is allowed to execute them.
