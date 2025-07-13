
# Astra Luxury Travel — Travel Agent Assignment Model

**Author:** Matt Snell  
**Date:** July 13, 2025

---

## Overview

This SQL-based algorithm matches prospective space travel customers with the best available travel agents based on historical performance and alignment. Designed to be run in real-time as a stored procedure, the algorithm ensures optimal agent assignment for customer satisfaction and business impact.

---

## Inputs to the Stored Procedure

- `Destination` (e.g., Mars, Europa)
- `LaunchLocation` (e.g., Cape Canaveral)
- `CommunicationMethod` (e.g., Email, HoloCall)
- `LeadSource` (e.g., Referral, Web)
- `TopN` (optional): Number of top agents to return

---

## Scoring Model

Each agent is scored using the following components:

| Factor                   | Description                                                               | Weight |
|--------------------------|---------------------------------------------------------------------------|--------|
| Avg Customer Rating      | Historical average from customer reviews                                  | 0.30   |
| Success Rate             | % of bookings confirmed vs. assigned                                      | 0.20   |
| Communication Match      | % of agent’s history using the given communication method                 | 0.15   |
| Lead Source Match        | % of agent’s history involving the given lead source                      | 0.15   |
| Destination Match        | % of bookings for the given destination                                   | 0.10   |
| Launch Location Match    | % of bookings from the given launch site                                  | 0.10   |

These are computed using SQL CTEs and aggregated into a weighted score.

---

## Output

The procedure returns a ranked list of travel agents, including:

- Agent Info (Name, Job Title, Department, Tenure)
- Match Ratios
- Score
- Rank (computed using `RANK()` over score)

---

## Validations

The stored procedure checks for valid input values based on existing data:
- Destination must exist in bookings.
- Launch Location must exist in bookings.
- Communication Method must exist in assignment history.
- Lead Source must exist in assignment history.

If any are invalid, a custom error is thrown to ensure integrity.

---

## Future Enhancements

To improve flexibility, accuracy, and scalability, consider the following:

### 1. **Historical Weight Tracking**
- Move hardcoded weights into a `weights_history` table with:
  - `Metric`, `Weight`, `StartDate`, `EndDate`
- Allows the model to evolve over time and enables A/B testing of weight schemes.

### 2. **Busyness Load Factor**
- Add a penalty for over-assigned agents.
- Use a `CurrentAssignments` metric or time-based rate of active work.
- Could reduce burnout and improve customer experience.

### 3. **Model Transparency Dashboard**
- Build a UI to visualize score components per agent.
- Allow business users to tune weights temporarily.

### 4. **Support for Time Decay**
- More recent interactions could weigh more heavily than old ones.

### 5. **Machine Learning Extension**
- Use XGBoost or logistic regression to learn optimal weights from booking outcomes.
- The SQL output could serve as features for model training.

### 6. **Performance Optimization**
- Materialize the CTEs if this is run at scale.
- Index foreign key joins (especially `AssignmentID`, `AgentID`)

---

## Time Budget Commentary

Given the 1-hour time constraint noted in the prompt, this solution focuses on producing a reliable, parameterized SQL-based algorithm that’s transparent, scalable, and suitable for production iteration. Advanced features are outlined but left for future phases.

---
