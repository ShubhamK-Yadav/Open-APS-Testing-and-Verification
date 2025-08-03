## Overview

This project focuses on analysing and modelling part of the OpenAPS (Open Artificial Pancreas System) oref0 algorithm for safety assessment purposes. OpenAPS is an open-source system that integrates:

- A Continuous Glucose Monitor (CGM)
- An Insulin Pump
- A Phone App

The system automatically adjusts insulin delivery based on CGM readings and algorithm calculations. Since incorrect dosages can be dangerous, the system is safety-critical. Our task is to model part of the algorithm, define tests, and analyse a mathematical condition using Z3.

Reference Design: (OpenAPS Design Details)[https://openaps.org/reference-design/]

### Tasks

#### 1. EFSM Modelling

We model the behaviour described under "Medical Device Communication" from the OpenAPS reference design, specifically:

1. Read new blood glucose data from CGM.
2. Query insulin pump for current basal rate.
3. If query succeeds:
   - Call compute_updated_basal(bg, basal_rate).
   - If result < 0 → Call cancel_temp_basal().
   - Else, if result ≠ current → Call update_temp_basal(new_value).
4. Confirm pump acknowledgment.
5. Query recent activity to ensure changes took effect.

### 2. Z3 Constraint Analysis

From the "Basic Overnight Operation (oref0)" section:
- Eventual BG is computed as:
  eventual_bg = current_bg - (ISF * IOB)

Interested in cases where:
_eventual_bg > target
current_bg < target_

**Goal:**
Determine if there exist positive integer values for:
- BG (current blood glucose)
- ISF (Insulin Sensitivity Factor)
- IOB (Insulin On Board)
- target (target BG)
- eventual_bg that satisfy these conditions.
