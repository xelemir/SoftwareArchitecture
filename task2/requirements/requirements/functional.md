# MealSwipe — Functional Requirements (Task 7)

1. **FR-01 — Profile (Core).** The system shall save, display, and update the user's dietary exclusions and nutritional goals and restore them in later sessions for the same user. *Use case: Maintain profile.*

2. **FR-02 — Planning inputs (Core; budget Extension).** The system shall accept a positive integer number of meals X, positive integer servings S (default 1), and optional positive cooking-time and calorie/protein targets. Nutrient targets apply per person across the X meals. When pricing is enabled, it shall also accept an optional non-negative EUR budget. *Use case: Configure and generate a meal plan.*

3. **FR-03 — Input validation (Core).** The system shall reject non-numeric, non-finite, or out-of-range inputs, identify the affected field and permitted values, and retain the last valid saved state. *Use case: Maintain profile; configure a meal plan.*

4. **FR-04 — Catalog import (Core; prices Extension).** The system shall import recipes with unique IDs, names, instructions, servings, cooking times, ingredients/quantities/units, dietary/allergen metadata, and per-serving calories/protein, reporting records with missing required fields or invalid values. Missing prices shall not prevent core use. *Use case: Import recipes.*

5. **FR-05 — Hard filtering (Core).** The system shall exclude recipes violating saved dietary exclusions or the entered cooking-time maximum before selecting questions, showing dishes, or generating plans. Missing or contradictory metadata needed to check an exclusion shall make the recipe ineligible; preferences and evaluator output shall not override exclusions. *Use case: Discover preferences; swipe dishes; generate a meal plan.*

6. **FR-06 — Recipe presentation (Core; costs Extension).** The system shall show each recommended dish's name, cooking time, per-serving nutrition, and health evaluation, with ingredients and instructions available in its details; estimated costs shall appear only when pricing is enabled. *Use case: Swipe dishes; view health evaluation.*

7. **FR-07 — Session decisions (Core).** The system shall record coarse-question answers and recipe likes/dislikes in the current user's session; right means yes/like and left means no/dislike. A decided recipe shall not reappear as undecided in that session. *Use case: Discover preferences; swipe dishes.*

8. **FR-08 — No remaining choices (Core).** The system shall explain when no candidates or undecided dishes remain and offer answer revision, explicit constraint changes, or a new session, without silently relaxing exclusions or repeating dishes. *Use case: Discover preferences; swipe dishes.*

9. **FR-09 — Changed inputs (Core).** The system shall recompute the candidate pool and affected evaluations after an answer, dietary exclusion, or planning input changes, before presenting the next question/dish or generating a plan. Saved plans shall retain their original settings. *Use case: Maintain profile; discover preferences; generate a meal plan.*

10. **FR-10 — Adaptive preference discovery (Core).** The system shall offer coarse questions before dish swiping, selecting unanswered attributes that distinguish the remaining candidates. Yes shall retain matching recipes and no shall retain non-matches; all active answers apply to the hard-filtered pool. The user shall be able to revise answers or switch to dishes at any time. When no distinguishing question remains, the system shall offer the remaining dishes or the FR-08 explanation. *Use case: Discover preferences.*

11. **FR-11 — Rule-based health evaluation (Core).** The system shall compare per-serving calories/protein with each entered plan target divided by X and display actual values, comparison targets, and an explanation. It shall classify values within 10% as within target and others as below/above target; without targets it shall display nutrition without claiming target fit. *Use case: View health evaluation.*

12. **FR-12 — LLM evaluation (Extension).** The system shall support an LLM evaluator with the same UI result fields as the rule-based evaluator, without allowing it to change catalog nutrition, hard constraints, or plan arithmetic. *Use case: View health evaluation.*

13. **FR-13 — AI fallback (Extension, required with FR-12).** The system shall return a rule-based evaluation on timeout, unavailability, rejected requests, or invalid LLM results, identify its source, and allow discovery/planning to continue. *Use case: View health evaluation.*

14. **FR-14 — Meal-plan generation (Core; budget Extension).** The system shall generate exactly X distinct currently eligible liked recipes from the current preference pool, with S servings. It shall enforce an entered budget when pricing is enabled. Core generation shall work without a shopping list, price data, or an LLM. *Use case: Generate a meal plan.*

15. **FR-15 — Plan ranking (Core).** The system shall rank evaluated compliant plans by mean relative absolute deviation from entered nutrient targets (zero if none), then fewer previously used recipes, then lower total cooking time, with ties broken by ascending lexicographic comparison of sorted recipe-ID lists. Hard constraints shall take precedence over ranking. *Use case: Generate a meal plan.*

16. **FR-16 — Unfulfilled plan request (Core).** The system shall identify the detected shortfall or conflicting constraints when no compliant X-meal plan is found and offer further discovery or explicit input changes. A search failure shall not be presented as proof that no possible plan exists. *Use case: Generate a meal plan.*

17. **FR-17 — Plan summary (Core; costs Extension).** The system shall display selected recipes, servings, cooking times, and per-person calorie/protein totals across the plan; estimated total cost shall be shown only when pricing is enabled. *Use case: View a meal plan.*

18. **FR-18 — Target deviations (Core).** The system shall show each entered nutrient target's actual plan total, deviation, and whether it is within 10% of the target; unmet nutritional goals shall not silently relax dietary, time, or enabled budget constraints. *Use case: Generate a meal plan; view health evaluation.*

19. **FR-19 — Shopping-list aggregation (Extension).** The system shall derive an ingredient list from the plan, scale each recipe's quantities by S divided by its original servings, and combine identical ingredients in compatible units (g/kg, ml/l), displaying names, total quantities, and units. *Use case: View shopping list.*

20. **FR-20 — Shopping-list pricing (Extension).** The system shall calculate ingredient costs using one snapshot of catalog EUR unit prices, round each aggregated line half-up to cents, and sum the lines for shopping-list total, plan cost, and budget checks. It shall label costs as estimates and report missing prices without displaying a complete total or claiming budget compliance. *Use case: View priced shopping list; check budget.*

21. **FR-21 — Plan history (Core).** The system shall save generated plans on user request and use that user's saved recipe history for variety ranking under FR-15. *Use case: Save a meal plan.*

22. **FR-22 — Reopen plans (Core; list Extension).** The system shall reopen the same user's saved plans with their original recipes, servings, settings, and nutrition; when the shopping/pricing extension was used, its associated list and price snapshot shall also be retained. *Use case: Reopen a meal plan.*
