# Vireo Audio — Take-Home Assessment Submission

## 1. What did you build?

I built a small Python-based tool that produces a weekly first-response SLA report by **agent and shift**.

The tool:

- Validates the supplied ticket and agent data.
- Removes duplicate ticket records while retaining the preferred helpdesk record.
- Converts timestamps from UTC to IST.
- Applies the correct first-response target for each support channel.
- Uses the historical agent assignment at the time the ticket was created.
- Calculates SLA breaches by agent, shift, and week.
- Calculates the associated store-credit exposure.
- Uses Gemini to review a controlled sample of ticket messages and identify common ticket themes.

The main output is a weekly report that can be used by Support Operations to identify where response delays are concentrated.

---

## 2. What is the business outcome in number and money?

The analysis covered **11,200 unique tickets**.

- **2,440 tickets breached the first-response SLA.**
- Overall breach rate: **21.79%**
- Automatic store-credit exposure: **₹854,000**

The Morning shift accounted for:

- **2,018 breaches**
- **82.70% of all breaches**
- **32.22% Morning-shift breach rate**

The financial exposure is calculated directly from the policy:

**2,440 breaches × ₹350 = ₹854,000**

---

## 3. How do you know it works?

The solution contains several checks.

### Data checks

- Required columns are validated.
- Duplicate ticket IDs are identified.
- Helpdesk records are preferred over legacy records where duplicates exist.
- Timestamps are parsed consistently as UTC before conversion to IST.
- First-response timestamps are checked against ticket creation times.
- Historical agent assignments are matched using the ticket creation date.

### SLA checks

The SLA calculation uses the supplied channel targets:

| Channel | Target |
|---|---:|
| Chat | 15 minutes |
| Voice | 2 hours |
| Social | 4 hours |
| Email | 8 hours |

An exact response time equal to the target is treated as a pass.

### Financial check

The calculated store-credit exposure reconciles independently:

**2,440 breaches × ₹350 = ₹854,000**

### AI check

A controlled 40-ticket sample was submitted for AI classification.

- 40 requests attempted
- 14 successful structured classifications
- 26 API failures
- Successful outputs were checked for the required fields

The 26 failures were primarily caused by Gemini API rate/quota limitations.

I therefore do **not** report the 35% technical completion rate as AI accuracy.

---

## 4. What did you change, narrow, or push back on?

I deliberately kept the solution small.

The original requirement is fundamentally a reporting and operational-review problem, so I did not build a large AI system.

I narrowed the AI component to ticket-theme classification and kept all SLA and financial calculations deterministic.

I also avoided treating AI output as proof of the reason a ticket breached its SLA.

---

## 5. What is wrong with the handoff today?

The available information is spread across ticket records and the historical agent roster, while timestamps come from the helpdesk system in UTC and the operating shifts are defined in IST.

There are also duplicate records from the historical migration.

This makes a manual weekly review more difficult and increases the risk of inconsistent calculations.

The tool creates one repeatable process for cleaning the records, applying the SLA rules, assigning the correct historical shift, and producing the weekly report.

---

## 6. What did you deliberately leave out?

I deliberately did not build:

- A real-time dashboard
- Automated staff scheduling
- A recommendation to hire additional staff
- A customer-facing AI assistant
- A large retrieval or multi-agent system
- Automated root-cause claims
- Tier 1 vs Tier 2 volume comparisons
- Claims linking SLA breaches directly to changes in P&L credits or CSAT

These were outside the core requirement or could introduce unsupported conclusions.

---

## 7. What did you find that was not explicitly asked for?

The analysis showed that the **Morning shift accounted for 82.70% of all observed SLA breaches**.

The AI-assisted review of a small sample also surfaced recurring themes around:

- Delivery and shipping
- Technical troubleshooting
- Delivery/logistics dependencies

These themes should be treated as areas for investigation rather than confirmed causes of SLA breaches.

---

## 8. How did you use AI?

AI was used during development to help with:

- Designing the ticket-classification structure
- Creating the classification prompt
- Producing structured JSON output
- Debugging the API integration
- Designing retry and validation logic
- Reviewing implementation decisions

In the final tool, **Gemini 2.5 Flash** is used only for classifying free-text ticket themes.

The following calculations are performed deterministically in Python:

- SLA targets
- First-response time
- SLA breach status
- Agent/shift assignment
- Weekly aggregation
- Credit exposure

This separation keeps the business calculations reproducible.

---

## 9. What AI approaches did not work?

I initially tested the built-in Google Colab AI interface for batch classification.

The approach was not reliable for this workload because requests began failing due to quota/cost limitations.

I also encountered Gemini API rate/quota failures during the final evaluation sample.

Rather than hiding these failures or repeatedly retrying indefinitely, I documented them and kept the successful structured classifications as qualitative evidence.

The final solution does not depend on AI for the core SLA calculation.

---

## 10. What would you hand to Neha on Monday morning?

I would hand over:

1. **Weekly agent SLA report** — showing ticket volume, breaches, breach rate, and credit exposure.
2. **Weekly shift SLA report** — showing where breaches are concentrated across Morning, Day, and Night shifts.
3. **One-page operational memo** — highlighting the 21.79% overall breach rate, ₹854,000 exposure, and Morning-shift concentration.

The first operational review should focus on understanding the Morning-shift delays and reviewing both high-volume and high-breach-rate cases.

---

## 11. What is the main recommendation?

Start with the **Morning shift**, because it contains the largest concentration of observed SLA breaches.

Use the agent report to distinguish between:

- People handling a large number of tickets, and
- People with a high percentage of missed responses.

Then review the delayed tickets for recurring operational problems before deciding on any process changes.

The data identifies **where to investigate first**; it does not by itself establish why the delays occurred.

---

## 12. Honest time spent

Approximately **5 hours** were spent building, testing, validating, and documenting the solution.

The implementation was intentionally kept focused on the requested business outcome rather than expanding into unnecessary system complexity.

---

## 13. Repository

The repository contains the reproducible analysis notebook and documentation.

Raw assessment data and private/generated files are excluded from the repository.
