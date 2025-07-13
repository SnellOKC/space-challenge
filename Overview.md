# 🛰️ Agent Assignment Model: Overview & Approach

**Objective:**  
Automatically assign incoming space travel leads to the most qualified travel agents, optimizing for customer experience, conversion likelihood, and efficiency.

---

## Inputs Considered
Each lead includes:
- **Destination**
- **Launch Location**
- **Preferred Communication Method**
- **Lead Source**

These are matched against historical agent activity to calculate a **personalized compatibility score** for each agent.

---

## Scoring Model

Our scoring model uses six weighted factors, each normalized as a ratio:

| Factor                      | Description                                                                 |
|----------------------------|-----------------------------------------------------------------------------|
| Customer Service Rating     | Agent’s historical average rating from customers                           |
| Booking Success Rate        | % of bookings that successfully completed (confirmed status)               |
| Communication Method Match  | % of past assignments using the same communication method                  |
| Lead Source Match           | % of past assignments with the same lead source                            |
| Destination Familiarity     | % of assignments that match the customer’s destination                     |
| Launch Location Familiarity | % of assignments from the same launch location                             |

These are combined into a single **composite score** using configurable weights:

```sql
Score = 
  0.30 * CustomerRating +
  0.20 * SuccessRate +
  0.15 * CommMatch +
  0.15 * LeadMatch +
  0.10 * DestMatch +
  0.10 * LaunchMatch
```

---

## ⚙Implementation Details

- The model is implemented as a **MySQL stored procedure**.
- It accepts the customer’s lead data as input and returns a **ranked list of matching agents**.
- The optional `TopN` parameter allows consumers to limit the number of results returned.
- The SQL uses CTEs for modularity and clarity, making it easy to extend or tune.

---

## Error Handling

- If any input value does not exist in the historical data (e.g., an unknown destination), the procedure returns a clear error message using `SIGNAL`.
- This allows the calling application to surface actionable feedback or fallback logic.

---

## Design Philosophy

- **Transparent**: Uses interpretable ratios instead of opaque ML scores.
- **Flexible**: Weights can be tuned by analysts without rewriting SQL logic.
- **Scalable**: All logic is set-based and performant for larger agent pools.
- **Maintainable**: Uses modular, readable SQL with minimal nesting or duplication.