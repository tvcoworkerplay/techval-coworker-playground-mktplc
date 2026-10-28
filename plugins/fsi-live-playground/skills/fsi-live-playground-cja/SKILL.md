---
name: fsi-live-playground-cja
title: "FSI Live Playground CJA Skill"
description: >
  Canonical rules for FSI Live Playground mortgage analysis in CJA: data view,
  project, segments, the Step 1 → Step 2 → Submitted → Pre-Approved/Declined →
  Originated flow, rounding, and default attribution. Use for any mortgage
  rate, calculator, application, pre-approval, decline, or origination question.
metadata:
  dependencies:
    skills:
      - ref: cja
        required: true
---

# FSI Live Playground CJA Skill

These rules come from clarifications with the FSI Live Playground owner. They
override generic assumptions for every mortgage analysis.

## 1. Data view and project
- **Data view:** FSI Live (`dv_6ad90f1b90e988b35bd4d3c5`). Always use it. Never
  use another FSI data view (FSI Data View, FSI Team Dataview, FSI - Lender).
- **Workspace project:** FSI Live (`6ad8d25f98b9a067abd987a9`). Use it as the
  reference project and as the place to point users who want to visualize
  results. If the user can't access it, say so and continue with the rules
  below.
- **Date range:** the user must specify it. If the prompt has no date range,
  ask for one before running anything. Never assume a default.

## 2. Mortgage flow (strict)
Research → Step 1 → Step 2 → Submitted → Pre-Approved **or** Declined →
Originated

| Stage | Component | Rule |
|---|---|---|
| Research | Page Name = "Mortgage Rates" or "Tool \| Mortgage Calculator" | Optional context; does **not** have to come before the application |
| Step 1 | Mortgage Application: Step 1 | Entry to the pre-approval application |
| Step 2 | Mortgage Application: Step 2 | Everyone at Step 2 also did Step 1 |
| Submitted | Mortgage Application Submitted | Everyone who submitted did Step 1 and Step 2 |
| Pre-Approved | Mortgage Pre-Approvals | Subset of Submitted; never overlaps Declined |
| Declined | Mortgage Declined | Subset of Submitted; Pre-Approved + Declined = Submitted |
| Originated | Mortgage Originations | Subset of Pre-Approved (approved and funded) |

- "Mortgage Calculator" is the only affordability tool. Don't look for others.
- Use **Page Name** (the "Modified for FSI Live Playground" version), not Page
  Details. "Mortgage Rates" is intentionally derived from "App | Account Type
  Details". It's demo data, not an error, so don't flag it.
- Identify steps only through the Step 1 / Step 2 **metrics**, not page names.
- Ignore **Event Type** (it only contains media events).
- Don't use "Application Completed" as the submit step.

**Drop-off formulas** (person level):
- stage conversion = stage people ÷ previous stage people × 100
- drop-off = 100 − stage conversion
- approval rate = Pre-Approved ÷ Submitted; originate rate = Originated ÷
  Pre-Approved

**Fallouts:** use Step 1 → Step 2 → Submitted → Pre-Approved → Originated
(Person container). Demo steps added for submitters share a timestamp with
their order, so check any strict-sequence fallout before presenting it.

## 3. Canonical segments
Reuse these Person-level segments (description starts with "Canonical FSI Live
Playground"):

| Segment | Definition |
|---|---|
| Mortgage Researchers | Page Name = Mortgage Rates or Tool \| Mortgage Calculator |
| Mortgage Application Starters | Step 1 or Step 2 exists |
| Mortgage Application Submitters | Submitted exists |
| Mortgage Application Started, Not Submitted | Step 1 or Step 2 exists AND exclude Submitted |
| Mortgage Pre-Approved | Pre-Approvals exists |
| Mortgage Application Declined | Declined exists |
| Mortgage Originated | Originations exists |
| Mortgage Researchers - Started, Not Submitted | Researchers AND (Step 1 or Step 2) AND exclude Submitted (any order) |

**Fallback:** if a segment isn't available to the current user (not shared or
deleted), rebuild it as a one-off filter from the definition above and say
that you did. Don't create new saved segments unless asked.

## 4. Numbers and rounding
- All mortgage KPIs are demo data from derived fields that output whole events
  (based on Person ID endings), so totals should already be whole numbers.
- **Safety net:** if any mortgage KPI comes back with a fraction, round event
  totals to whole numbers and percentages to 1 decimal, computing rates from
  the unrounded values. Note once: "Event totals are derived demo values and
  are shown rounded."
- **Report limit:** CJA allows at most 5 derived-field metrics or dimensions
  per report. All six mortgage KPIs are derived, so split them across two
  reports or tables.

## 5. Attribution
- If a question needs an attribution model (e.g. crediting channels,
  campaigns, or pages for Submitted, Pre-Approvals, Declined, or Originations)
  and the user didn't specify one, use **Algorithmic** with a **Person**
  container and a **30-day** lookback.
- Always state the model in the answer, e.g. "Attribution: Algorithmic,
  Person container, 30-day lookback (default — no model specified)."
- If the user specifies a model, use it and state it.
- Plain counts, people, and segment results don't need an attribution note.

## 6. Answer checklist
Every mortgage answer states:
1. Data view (FSI Live) and the date range used
2. The stage definitions applied (or a link to the canonical segments)
3. Attribution model, if one was used
4. Any fallback taken (segment rebuilt, project inaccessible, values rounded)
