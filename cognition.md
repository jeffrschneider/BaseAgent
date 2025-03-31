# Prompts for ADE Pattern with Looping and Exit Conditions

## Assumptions

- Looping: Occurs when a step fails or needs refinement (e.g., resource unavailable, execution unsuccessful), sending the process back to an earlier stage.
- Exit Conditions: Triggered by exceeding effort (e.g., time, resources) or failure limits (e.g., 3 retries), halting the process with a fallback response.
- Thresholds: Configurable defaults (e.g., 3 attempts, 30 seconds total effort) adjusted by triage stakes/urgency.

---

## 1. Prompt: "Triage Request Classifier"
- Stage: Triage and Scope Assessment
- When Invoked: At the start (T=0), once per request.
- Purpose: Assess complexity and set scope, including effort/failure limits.

```
Given the request: "[USER_INPUT]", classify it:
- Simple Statement (e.g., "Hello")
- Simple Query (e.g., "5 + 7")
- Moderate Task (e.g., "What’s the weather?")
- Complex Task (e.g., "Plan a budget trip")

Evaluate:
- Urgency: High (immediate) or Low (can wait)?
- Stakes: Low (trivial) or High (significant)?
- Resources: None, Basic, or Advanced?

Set thresholds:
- Max Attempts: Simple=1, Moderate=2, Complex=3
- Max Effort: Simple=5s, Moderate=15s, Complex=30s

Output: [Category, Urgency, Stakes, Resources, Max Attempts, Max Effort], recommend ADE steps: Minimal, Partial, or Full.
```

- Looping: None (runs once).
- Exit: None (initial step).
- Example: "Plan a budget trip" → [Complex, Low, High, Advanced, 3, 30s] → Full ADE.

---

## 2. Prompt: "Goal and Benefit Evaluator"
Stage: Goal Clarification and Benefit Analysis
When Invoked: T=1, if triage scope is Moderate or Complex.
Purpose: Define goal and beneficiaries, with a loop-back if unclear.

```
For request: "[USER_INPUT]" with profile [CATEGORY, URGENCY, STAKES, RESOURCES, MAX_ATTEMPTS, MAX_EFFORT]:
- Define goal (e.g., "Plan a cost-effective trip").
- Assess value: Low/Medium/High.
- Identify beneficiaries: User/Agent/Society?
- If unclear, loop back with: "Please clarify [specific detail]."
- Track attempts (start at 1).
```

- Loop: If goal unclear after MAX_ATTEMPTS, exit.
- Exit: "Sorry, I can’t proceed without a clear goal."
- Output: [Goal, Value, Beneficiaries, Attempt Count].
- Looping: If goal is vague (e.g., "Plan a trip" → "Budget or luxury?"), loops to ask user, up to Max Attempts.
- Exit: After Max Attempts (e.g., 3), stops with fallback message.
- Example: "Plan a budget trip" → [Affordable travel, High, User+Agent, 1] (no loop needed).

## 3. Prompt: "Resource and Creativity Checker"
- Stage: Resource and Constraint Assessment with Creative Resolution
- When Invoked: T=2, if triage flags resources/constraints.
- Purpose: Check resources and resolve gaps, looping if solutions fail.

```
For request: "[USER_INPUT]" with profile [PROFILE]:
- List resources: [e.g., web, memory].
- List constraints: [e.g., no budget data].
- Propose solutions: Substitute/Augment/Delegate.
- Test solution feasibility (e.g., "Can I access this?").
- If unfeasible, loop back to propose new solution, increment attempts.

```

- Loop: Retry up to MAX_ATTEMPTS if solutions fail.
- Exit: If MAX_ATTEMPTS or MAX_EFFORT exceeded, "I lack resources to proceed."

- Looping: If a solution fails (e.g., web down), loops to try another (e.g., memory), up to Max Attempts.
- Exit: After Max Attempts (e.g., 3) or Max Effort (e.g., 30s), stops with error.
- Example: "Plan a budget trip" → [Web, No budget, Ask user, 1] (loops if user doesn’t reply).

## 4. Prompt: "Option and Risk Weigher"
- Stage: Option Generation with Risk/Reward Analysis
- When Invoked: T=3, if triage flags Moderate/Complex with options.
- Purpose: Evaluate options, looping if top option seems risky.

```
For request: "[USER_INPUT]" with profile [PROFILE]:
- List options: [e.g., "Search flights," "Guess"].
- Assess each: Reward (1-10), Risk (1-10), Probability (0-1).
- Score: Reward - (Risk × Probability).
- If top score < 3 (low confidence), loop back to generate more options.
```
- Looping: If scores are poor (e.g., all < 3), loops to rethink options, up to Max Attempts.
- Exit: After Max Attempts (e.g., 3) or Max Effort, stops with fallback.
- Example: "Plan a budget trip" → [Flights: 6.6, Bus: 2.2, Flights, 1] (no loop).

---

## 5. Prompt: "Plan and Memory Optimizer"
- Stage: Planning and Optimization with Memory Integration
- When Invoked: T=4, if triage flags Complex or memory use.
- Purpose: Plan steps with memory, looping if plan seems flawed.

```
For request: "[USER_INPUT]" with option [TOP_OPTION]:
- Break into steps: [e.g., "Search, confirm, report"].
- Optimize: Order, trade-offs.
- Check memory: Past successes/failures.
- If plan risks failure (e.g., memory flags "Search failed before"), loop to adjust.
```

- Loop: Retry up to MAX_ATTEMPTS if plan unviable.
- Exit: If MAX_ATTEMPTS or MAX_EFFORT exceeded, "Can’t plan effectively."
- Output: [Plan, Memory Insights, Attempt Count].
- Looping: If memory suggests failure (e.g., "Tool X slow"), loops to tweak plan, up to Max Attempts.
- Exit: After Max Attempts (e.g., 3) or Max Effort, stops with error.
- Example: "Plan a budget trip" → [Search flights, confirm, report], [Flights worked], 1 (no loop).

---

## 6. Prompt: "Execute and Monitor Driver"
- Stage: Execution with Monitoring and Adaptation
- When Invoked: T=5, always (scaled to scope).
- Purpose: Act and adapt, looping on failure.
 
```
For request: "[USER_INPUT]" with plan [PLAN] or action:
- Execute: Perform steps or action.
- Monitor: Time, resources, success (Success/Fail).
- If Fail, loop back to prior step (T=2, 3, or 4 based on issue), increment attempts.
- Track total effort (time spent).

```
- Looping: On failure (e.g., search fails), loops to Resource (T=2), Option (T=3), or Plan (T=4), up to Max Attempts.
- Exit: After Max Attempts (e.g., 3) or Max Effort (e.g., 30s), stops with fallback.
- Example: "Plan a budget trip" → [$500 flight], [10s, 1, Success] (no loop).

---
  
## 7. Prompt: "Reflect and Memory Updater"
- Stage: Reflection, Memory Update, and Outcome Evaluation
- When Invoked: T=6, if triage flags Moderate/Complex.
- Purpose: Reflect and learn, no looping here (final step).

```
  For request: "[USER_INPUT]" with result [RESULT], log [LOG]:
- Evaluate: Goal met? Efficient?
- Risk/Reward: Justified?
- Update memory: [e.g., "Flights worked, 10s"].
- Assess benefits: Who gained, how?
```
  
- Exit: If prior steps exceeded MAX_EFFORT, note "Over-effort" in memory.
- Output: [Evaluation, Memory Update, Benefits].
- Looping: None (end of process).
- Exit: Completes normally or notes over-effort/failure from prior steps.

---

**Example**: "Plan a budget trip" → [Success, good], [Flights fast], [User saved, Agent learned].
Clarified Flow with Looping and Exits
- T=0: Triage sets the stage (no loop).
- T=1 to T=4: Conditional steps with loops:
  - Goal: Loops for clarity (e.g., ask user again).
  - Resource: Loops for new solutions (e.g., try another tool).
  - Option: Loops for better options (e.g., rethink if risky).
  - Plan: Loops for viable plan (e.g., adjust based on memory).
- T=5: Execution loops back to T=2/3/4 on failure (e.g., tool fails → new resource/option/plan).
- T=6: Reflection finalizes (no loop, but records exits).
Exit Points: Each step checks Max Attempts (task-specific) and Max Effort (cumulative time across all steps). Fallbacks like "Can’t proceed" or "Failed after X tries" trigger when limits are hit.

---

**Example**: "Plan a budget trip" with Failure Loop
- T=0: [Complex, Low, High, Advanced, 3, 30s] → Full ADE.
- T=1: [Affordable travel, High, User+Agent, 1] (no loop).
- T=2: [Web, No budget, Ask user, 1] (user doesn’t reply → loop).
- T=2 (Loop): [Web, No budget, Estimate from memory, 2] (works).
- T=3: [Flights: 6.6, 1] (no loop).
- T=4: [Search, confirm, report], [Flights worked], 1 (no loop).
- T=5: [Search fails], [5s, 2, Fail] → Loop to T=2.
- T=2 (Loop): [Web down, Use memory, 3] (works).
- T=5 (Retry): [$500 flight], [15s total, 3, Success].
- T=6: [Success], [Memory: Web flaky], [User benefited].
Exit Triggered: If T=5 failed again (attempt 4 > 3), stops with "Failed after 3 tries."
