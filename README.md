# Railway Level-Crossing Control System (ARLCCS)

## Software Verification Lab — Constraints, Formalization & Violations

### System Overview

The Automated Railway Level-Crossing Control System (ARLCCS) controls road traffic when a train approaches, passes through, and clears a railway crossing. The system uses train-detection sensors, road barriers, warning lights, audible alarms, traffic signals, a safety-monitoring unit, and a control-center interface.

The main safety objective is to prevent road traffic from entering the crossing while a train is present or approaching and to ensure that barriers are opened only after the crossing has been confirmed safe.

---

# Task 1 — Identify Constraints

The following constraints represent rules that the ARLCCS must always satisfy.

| ID | Constraint in Simple English | Why the Constraint Is Necessary |
|---|---|---|
| **C1** | The barrier must not open while a train is present in the crossing. | Opening the barrier while a train is present could allow vehicles or pedestrians onto the railway track and cause a collision. |
| **C2** | When a train is approaching, the warning lights and audible alarm must be activated. | Road users need sufficient warning that a train is approaching and the crossing is becoming unsafe. |
| **C3** | When a train is approaching, the road barriers must be closed before the train reaches the crossing. | The barriers prevent road traffic from entering the crossing before the train arrives. |
| **C4** | The barriers must remain closed while the train is passing through the crossing. | Keeping the barriers closed prevents vehicles from entering the train's path during passage. |
| **C5** | The barriers may open only after the system confirms that the train has completely cleared the crossing. | A train may still be occupying the crossing even after one detection point becomes inactive, so clearance must be confirmed. |
| **C6** | If a train-detection sensor fails, the system must not open the barriers based on an uncertain train-clear signal. | A failed sensor can produce incorrect information. A safety system should fail safely rather than risk opening the crossing. |
| **C7** | If a barrier failure is detected, the system must activate a safety warning and notify the control center. | Operators must be informed immediately so that appropriate emergency action can be taken. |
| **C8** | If communication with the control center is lost, the crossing must continue operating in a safe state. | Loss of communication must not cause the barriers to open or otherwise create an unsafe crossing condition. |
| **C9** | The system must not allow road traffic to pass when a train is approaching or present. | Preventing road traffic from entering the crossing is the primary safety requirement. |
| **C10** | In an emergency condition, the system must activate the required warnings and keep the crossing in a safe state until the emergency is resolved. | Emergency conditions can make normal operation unreliable; the system must prioritize safety. |

---

# Task 2 — Formalize Constraints

Boolean variables used in the expressions:

- **Train_Present** = TRUE when a train is physically present at the crossing.
- **Train_Approaching** = TRUE when a train is approaching the crossing.
- **Train_Cleared** = TRUE when the system has confirmed that the train has completely cleared the crossing.
- **Barrier_Open** = TRUE when the road barrier is open.
- **Barrier_Closed** = TRUE when the road barrier is closed.
- **Warning_Active** = TRUE when warning lights/alarm are active.
- **Sensor_Failure** = TRUE when a train-detection sensor has failed.
- **Barrier_Failure** = TRUE when a barrier failure is detected.
- **Communication_Lost** = TRUE when communication with the control center is unavailable.
- **Emergency** = TRUE when an emergency condition exists.
- **Traffic_Allowed** = TRUE when road traffic is permitted to cross.
- **Control_Center_Alert** = TRUE when the control center has been notified.
- **Safety_State** = TRUE when the crossing is maintained in a safe state.

## Formal Constraints

### C1 — Barrier must not open while a train is present

**Formal expression:**

> Train_Present → ¬Barrier_Open

**Meaning:** If a train is present, the barrier must not be open.

---

### C2 — Warning must be active when a train is approaching

**Formal expression:**

> Train_Approaching → Warning_Active

**Meaning:** An approaching train must cause the warning system to activate.

---

### C3 — Approaching train requires a closed barrier

**Formal expression:**

> Train_Approaching → Barrier_Closed

**Meaning:** When a train is approaching, the road barrier must be closed.

---

### C4 — Barrier remains closed while train is present

**Formal expression:**

> Train_Present → Barrier_Closed

**Meaning:** The barrier must remain closed while the train is passing through the crossing.

---

### C5 — Barrier can open only after confirmed clearance

**Formal expression:**

> Barrier_Open → Train_Cleared

**Meaning:** Whenever the barrier is open, the system must already have confirmed that the train has completely cleared the crossing.

---

### C6 — Sensor failure prevents safe-clear opening

**Formal expression:**

> Sensor_Failure → ¬Barrier_Open

**Meaning:** If a train-detection sensor has failed, the barrier must not be opened based on uncertain detection information.

---

### C7 — Barrier failure requires a control-center alert

**Formal expression:**

> Barrier_Failure → Control_Center_Alert

**Meaning:** Whenever a barrier failure is detected, the control center must be notified.

---

### C8 — Communication loss must preserve a safe state

**Formal expression:**

> Communication_Lost → Safety_State

**Meaning:** Loss of communication must not cause the crossing to leave its safe operating state.

---

# Task 3 — Identify Constraint Violations

## Violation V1 — C1: Barrier opens while train is present

**Constraint:**

> Train_Present → ¬Barrier_Open

**Violation values:**

- Train_Present = TRUE
- Barrier_Open = TRUE

**What went wrong?**

A train is physically present at the crossing, but the barrier is open.

**How do we know the constraint is violated?**

The rule requires the barrier to be closed whenever Train_Present is TRUE. Since Barrier_Open is also TRUE, the required condition ¬Barrier_Open is false. Therefore, C1 is violated.

**Safety impact:** Road traffic could enter the crossing while the train is passing.

---

## Violation V2 — C2: Warning system remains inactive

**Constraint:**

> Train_Approaching → Warning_Active

**Violation values:**

- Train_Approaching = TRUE
- Warning_Active = FALSE

**What went wrong?**

A train has been detected approaching the crossing, but the warning lights and audible alarm have not been activated.

**How do we know the constraint is violated?**

The implication requires Warning_Active to be TRUE whenever Train_Approaching is TRUE. Because the warning system is FALSE, C2 is violated.

**Safety impact:** Drivers and pedestrians may not receive adequate warning.

---

## Violation V3 — C3: Barrier remains open for an approaching train

**Constraint:**

> Train_Approaching → Barrier_Closed

**Violation values:**

- Train_Approaching = TRUE
- Barrier_Closed = FALSE
- Barrier_Open = TRUE

**What went wrong?**

The system detects an approaching train but fails to close the road barrier.

**How do we know the constraint is violated?**

The condition Train_Approaching is TRUE, so Barrier_Closed must also be TRUE. It is FALSE, therefore C3 is violated.

**Safety impact:** Vehicles can enter the crossing before the train arrives.

---

## Violation V4 — C4: Barrier opens while train is passing

**Constraint:**

> Train_Present → Barrier_Closed

**Violation values:**

- Train_Present = TRUE
- Barrier_Closed = FALSE
- Barrier_Open = TRUE

**What went wrong?**

The train is still passing through the crossing, but the barrier has opened.

**How do we know the constraint is violated?**

Train_Present is TRUE, therefore Barrier_Closed must be TRUE. Since the barrier is open, the required condition is false.

**Safety impact:** A vehicle could enter the train's path.

---

## Violation V5 — C5: Barrier opens without confirmed train clearance

**Constraint:**

> Barrier_Open → Train_Cleared

**Violation values:**

- Barrier_Open = TRUE
- Train_Cleared = FALSE

**What went wrong?**

The system opens the barrier even though it has not confirmed that the train has completely cleared the crossing.

**How do we know the constraint is violated?**

The rule states that Barrier_Open implies Train_Cleared. Because the barrier is open while Train_Cleared is FALSE, C5 is violated.

**Safety impact:** The train may still be approaching, passing, or occupying part of the crossing.

---

## Violation V6 — C6: Barrier opens after sensor failure

**Constraint:**

> Sensor_Failure → ¬Barrier_Open

**Violation values:**

- Sensor_Failure = TRUE
- Barrier_Open = TRUE

**What went wrong?**

A train-detection sensor has failed, but the system opens the barrier anyway.

**How do we know the constraint is violated?**

When Sensor_Failure is TRUE, the barrier must not be open. Since Barrier_Open is TRUE, the rule is broken.

**Safety impact:** The system may incorrectly assume that the crossing is clear.

---

## Violation V7 — C7: Barrier failure is not reported

**Constraint:**

> Barrier_Failure → Control_Center_Alert

**Violation values:**

- Barrier_Failure = TRUE
- Control_Center_Alert = FALSE

**What went wrong?**

The system detects a barrier failure but does not notify the control center.

**How do we know the constraint is violated?**

The rule requires a control-center alert whenever Barrier_Failure is TRUE. Since no alert is generated, C7 is violated.

**Safety impact:** Operators may remain unaware of a dangerous hardware failure.

---

## Violation V8 — C8: Communication loss causes an unsafe state

**Constraint:**

> Communication_Lost → Safety_State

**Violation values:**

- Communication_Lost = TRUE
- Safety_State = FALSE

**What went wrong?**

Communication with the control center is lost and the crossing enters an unsafe state.

**How do we know the constraint is violated?**

The formal rule requires Safety_State to remain TRUE whenever communication is lost. Since Safety_State is FALSE, C8 is violated.

**Safety impact:** The crossing could become unsafe without remote monitoring or intervention.

---

# Summary of Verification

The eight formalized constraints define safety-related rules for the most important system states:

1. A train must never coexist with an open barrier.
2. An approaching train must activate warnings.
3. An approaching train must result in a closed barrier.
4. The barrier must remain closed while a train is present.
5. The barrier can open only after confirmed train clearance.
6. Sensor failure must prevent unsafe barrier opening.
7. Barrier failure must generate a control-center alert.
8. Communication loss must not place the crossing in an unsafe state.

A violation occurs whenever the actual system state makes the required logical implication false. These constraints therefore provide clear, testable conditions that can be used by a Software Verification Team to check whether the ARLCCS behaves safely under both normal and abnormal conditions.

## Overall Safety Principle

> **If there is any uncertainty about whether the crossing is safe, the system should remain in a safe state rather than open the crossing.**

This follows the principle of **fail-safe operation**, which is essential for an automated railway level-crossing control system.
