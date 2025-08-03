## Overview

This project analyses and models part of the **OpenAPS** (Open Artificial Pancreas System) `oref0` algorithm for safety assessment purposes.  
OpenAPS is an open-source system that integrates:

- **Continuous Glucose Monitor (CGM)**
- **Insulin Pump**
- **Phone App**

The system automatically adjusts insulin delivery based on CGM readings and algorithm calculations.  
Since incorrect dosages can be dangerous, the system is **safety-critical**.  
Our tasks include modelling part of the algorithm, defining tests, and analysing a mathematical condition using **Z3**.

**Reference Design:** [OpenAPS Design Details](https://openaps.org/reference-design/)

---

## Tasks

### 1. EFSM Modelling

We model the behaviour described under **"Medical Device Communication"** from the OpenAPS reference design:

1. Read new blood glucose data from the CGM.
2. Query the insulin pump for its current basal rate.
3. If the query succeeds:
   - Call `compute_updated_basal(bg, basal_rate)`.
   - If the result `< 0` → call `cancel_temp_basal()`.
   - Else, if the result ≠ current basal rate → call `update_temp_basal(new_value)`.
4. Confirm the pump acknowledgment.
5. Query recent activity to ensure the new temporary basal took effect.

---

### 2. Z3 Constraint Analysis

From the **"Basic Overnight Operation (oref0)"** section:

Eventual BG is computed as: `eventual_bg = current_bg - (ISF * IOB)`

Interested in cases where: 
- `eventual_bg > target`
- `current_bg < target`

**Goal:**  
Determine if there exist **positive integer values** for:

- `BG` (current blood glucose)
- `ISF` (Insulin Sensitivity Factor)
- `IOB` (Insulin On Board)
- `target` (target BG)
- `eventual_bg`
that satisfy these conditions.
