# Evaluation Summary

This document summarises the evaluation results of the LLM-enabled humanoid robot system.

The system was evaluated across multiple pipeline stages rather than only final task completion. The evaluation focused on perception, structured LLM output generation, Planner/Controller validation, IK/FK-supported motion planning, and physical execution.

---

## Evaluation Scope

The evaluation covered the following stages:

```text
Vision System
LLM Tool-Call Generation
Planner / Controller Validation
IK / FK Motion Planning
Physical Object Approach
Physical Pick Attempts
Failure Handling
```

The evaluation focused on whether the robot could:

- detect objects in the environment,
- identify target objects,
- use head movement for object search,
- centre detected objects,
- interpret natural language commands,
- generate structured JSON-style tool calls,
- validate or repair invalid LLM outputs,
- prevent unsafe physical execution,
- generate IK/FK-supported motion attempts,
- attempt physical object interaction,
- handle failure cases safely.

---

## Evaluation Metrics

The system was evaluated using the following metrics:

| Evaluation Category | Metric | Assessment Criteria |
|---|---|---|
| Perception Accuracy | Object Detection Accuracy | Target object is detected with the correct label and sufficient confidence. |
| Spatial Reliability | Depth and Centre Availability | Depth value and centre-point information are available for motion planning. |
| Reasoning Accuracy | Tool-Calling Accuracy | The LLM selects the correct tool and provides suitable arguments. |
| Output Structure | JSON Validity | The LLM output is parseable and follows the required JSON structure. |
| Validation Quality | Planner/Controller Decision | The action is correctly approved, repaired, rejected, or clarified. |
| Motion Planning Feasibility | IK/FK Success | A valid pose is generated with acceptable FK error or safe reachability status. |
| Execution Performance | Task Execution Success | The task is completed or safely progresses through partial execution. |
| Responsiveness | Response and Execution Time | The system responds and starts or executes action within an acceptable time. |
| Robustness | Retry/Recovery Behaviour | The system retries, rescans, asks clarification, reports failure, or safely stops. |
| Error Analysis | Failure Source Classification | Failed attempts are classified by source: perception, LLM, validation, IK/FK, or execution. |

---

## Evaluation Stages and Recorded Outputs

| Evaluation Stage | Recorded Outputs | Purpose |
|---|---|---|
| Vision System | Label, confidence, depth, centre point | Evaluate object perception and spatial information availability. |
| LLM Layer | JSON output, response type, tool, arguments | Evaluate structured command generation and tool-call behaviour. |
| Planner / Controller | Validation result, correction, clarification, rejection | Evaluate safety, consistency, and state-aware task validation. |
| IK/FK Pipeline | Target coordinate, selected pose, FK error, reachability status | Evaluate motion planning feasibility and pose generation. |
| Physical Execution | Success/failure status, retry status, execution time | Evaluate task progression, robustness, and safe execution. |

---

## 1. Vision System Evaluation

The Vision System was evaluated for object detection, target detection, head-movement-based search, and object centring.

| Test Type | Number of Trials | Successful Detections | Failed Detections | Success Rate | Average Confidence |
|---|---:|---:|---:|---:|---|
| Visible object detection | 20 | 18 | 2 | 90% | 0.75–0.80 |
| Target object detection | 20 | 19 | 1 | 95% | 0.75–0.80 |
| Object search with head movement | 20 | 15 | 5 | 75% | 0.65–0.80 |
| Object centring | 20 | 17 | 3 | 85% | 0.65–0.80 |

### Observation

The Vision System performed strongly in static visible object detection and target object detection. The highest result was achieved in target object detection with a 95% success rate.

Object search with head movement produced a lower success rate of 75%. This was mainly affected by camera movement, viewing angle changes, vibration, and confidence variation.

Overall, the Vision System provided usable real-time object-level context for the LLM reasoning layer, State/Memory updates, and Planner/Controller validation.

---

## 2. Raw LLM Output Evaluation

The raw LLM output was evaluated with the Planner and Controller layers disabled. This was done to test whether the fine-tuned model could independently convert user commands into structured JSON outputs.

The test set included 29 prompts across chat, tool_call, clarify, and refuse output types.

### Raw LLM Evaluation Prompt Categories

| Prompt Category | Example Query Type | Expected Behaviour |
|---|---|---|
| General interaction | Greeting, identity, capability questions | Generate chat response |
| Visible object query | Asking what objects are visible | Call perception-related tool |
| Movement / action command | Move, pick, search, or bring object | Generate relevant tool_call |
| Ambiguous reference | “Pick that up”, “Move there” | Generate clarify response |
| Unsupported / unsafe request | Direct motor control or impossible action | Generate refuse response |

### Raw LLM Output Results

| Test Type | Number of Tests | Successful Outputs | Failed Outputs | Success Rate |
|---|---:|---:|---:|---:|
| Type Accuracy | 29 | 23 | 6 | 79.31% |
| Tool-Calling Accuracy | 9 | 7 | 2 | 77.78% |
| Argument Accuracy | 2 | 2 | 0 | 100% |
| Refusal / Safety Handling | 6 | 1 | 5 | 16.67% |
| Clarification Handling | 6 | 5 | 1 | 83.33% |
| JSON Validity | 29 | 29 | 0 | 100% |

### Observation

The model achieved 100% JSON validity, meaning all tested outputs were parseable and followed the required JSON structure.

However, the model was not reliable enough for direct robot execution. The weakest area was refusal/safety handling, with only 16.67% success. Some unsupported or unsafe motor-control requests were still generated as executable tool calls.

This confirms that the LLM should not directly control the physical robot. A deterministic validation layer is required between the LLM and the execution system.

---

## 3. Planner / Controller Validation Evaluation

The Planner and Controller were evaluated to measure whether raw LLM outputs could be validated, repaired, rejected, clarified, or safely passed to execution.

| Test Type | Number of Tests | Successful Outputs | Failed Outputs | Success Rate |
|---|---:|---:|---:|---:|
| Overall validation handling | 29 | 29 | 0 | 100% |
| Commands passed without intervention | 29 | 6 | 23 | 20.69% |
| Commands requiring intervention | 23 | 23 | 0 | 100% |
| Clarification handling | 6 | 6 | 0 | 100% |
| Unsafe execution prevention | 5 | 5 | 0 | 100% |

### Observation

The Planner/Controller layer significantly improved system reliability.

Only 20.69% of commands could pass without intervention, while 23 out of 29 commands required some form of validation, correction, clarification, rejection, or safety handling.

The system achieved 100% success in:

- overall validation handling,
- commands requiring intervention,
- clarification handling,
- unsafe execution prevention.

This shows that the Planner/Controller layer is essential for safe physical execution and supports the use of the LLM as a high-level reasoning component rather than a direct motor controller.

---

## 4. IK/FK, Motion Planning, and Physical Execution Evaluation

IK/FK and motion planning were evaluated to understand whether target coordinate information from the Vision System could be converted into executable joint-level motion commands.

The motion planning stage included:

```text
Vision target detection
     |
     v
Spatial mapping
     |
     v
IK target coordinate estimation
     |
     v
Candidate pose generation
     |
     v
FK-based pose checking
     |
     v
Serial command generation
     |
     v
Arduino-based low-level execution
```

Physical execution was influenced by:

- pose generation,
- target reachability,
- gripper alignment,
- object height,
- depth uncertainty,
- mechanical tolerance,
- fixed-base approach angle,
- limited 5-DOF arm flexibility.

---

## 5. Physical Pick Attempt Results

Physical pick attempts were evaluated over 100 trials.

| Outcome Category | Number of Trials | Rate | Interpretation |
|---|---:|---:|---|
| Successful pick | 10 | 10% | Object was grasped and held successfully. |
| Object grasped but dropped | 21 | 21% | The gripper contacted and lifted/held the object briefly, but the object was dropped. |
| Target considered unreachable | 15 | 15% | The system could not safely reach the target or reported that the object was too far. |
| Reached target area but failed to grasp | 54 | 54% | The robot reached the target area, but the final grasp failed. |
| Total | 100 | 100% | Overall physical pick attempt evaluation set. |

### Observation

The physical pick attempt results show that the main limitation is final grasp stability rather than structured reasoning or IK/FK pose generation.

The robot was able to approach the target area in many trials, but stable grasp consistency remained low.

The main causes were:

- fixed-base geometry,
- limited 5-DOF flexibility,
- gripper alignment sensitivity,
- mechanical tolerance,
- object height variation,
- limited approach angles.

Physical manipulation was therefore the weakest part of the system, while the perception-reasoning-validation pipeline performed more consistently.

---

## Summary of Results

| System Stage | Main Result |
|---|---|
| Visible object detection | 90% success |
| Target object detection | 95% success |
| Object search with head movement | 75% success |
| Object centring | 85% success |
| Raw LLM JSON validity | 100% |
| Raw LLM type accuracy | 79.31% |
| Raw LLM tool-calling accuracy | 77.78% |
| Raw LLM refusal/safety handling | 16.67% |
| Planner/Controller overall validation | 100% |
| Planner/Controller unsafe execution prevention | 100% |
| Physical successful pick | 10% |
| Object grasped but dropped | 21% |
| Reached target but failed grasp | 54% |

---

## Key Findings

- The Vision System provided usable object-level context for the reasoning and validation layers.
- The LLM successfully learned the structured JSON output format.
- Raw LLM output was not reliable enough for direct robot execution.
- Planner/Controller validation was necessary for safety, clarification, correction, and state-aware execution.
- The Planner/Controller layer prevented unsafe execution successfully in the tested cases.
- The physical manipulation stage was significantly weaker than the perception and reasoning stages.
- The main physical limitation was grasp stability, not tool-call generation.
- Mechanical improvements are required to improve pick reliability.

---

## Current Limitations

The current system is a functional prototype, but it has several limitations:

- limited physical grasp reliability,
- limited 5-DOF manipulation flexibility,
- fixed-base structure,
- gripper alignment sensitivity,
- object height variation,
- mechanical tolerance from 3D-printed parts,
- no force/torque sensing in the gripper,
- limited closed-loop visual servoing during grasp execution,
- controlled indoor testing environment only.

---

## Future Evaluation Improvements

Future evaluation work should include:

- larger command-level benchmark dataset,
- automatic logging for all task attempts,
- ROS2-based logging and replay,
- physical pick success improvement tests,
- comparison between raw LLM output and validated execution,
- latency measurement for perception, LLM, validation, and execution stages,
- object detection accuracy under different lighting and object positions,
- simulation-based repeatable evaluation,
- improved gripper and wrist-camera alignment testing.

---

## Conclusion

The evaluation shows that the LLM-enabled humanoid robot is more successful as an integrated perception-reasoning-validation system than as a fully reliable manipulation platform.

The strongest results were achieved in:

- target object detection,
- JSON validity,
- Planner/Controller validation,
- unsafe execution prevention.

The weakest result was physical pick reliability, with only 10% successful pick completion. This indicates that the main future improvement should focus on mechanical design, gripper alignment, closed-loop visual servoing, and manipulation reliability.