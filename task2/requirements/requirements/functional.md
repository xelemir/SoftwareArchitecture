# MealSwipe — Functional Requirements (Task 7)

1. **FR-01 — User registration and authentication (Core).** The system shall allow a user to register an account with unique credentials, sign in to establish an authenticated session, and sign out. Access to user-specific profiles, swipe sessions, saved plans, history, and shopping lists shall be restricted to the authenticated owner, and invalid or duplicate credentials shall be rejected with clear error feedback.

2. **FR-02 — Profile (Core).** The system shall save, display, and update the authenticated user's dietary exclusions (from the supported allergen and diet set; medical diets are out of scope) and default nutritional goals (calorie and protein targets), and restore them in later sessions for the same user.

3. **FR-03 — Planning inputs (Core).** The system shall accept a positive integer number of meals X (1–14), positive integer servings S (1–10, default 1), an optional positive cooking-time maximum, optional positive calorie/protein targets (defaulting from saved profile nutritional goals when set), and an optional non-negative EUR budget. Nutrient targets apply per person across the X meals.

4. **FR-04 — Input validation (Core).** The system shall reject non-numeric, non-finite, or out-of-range inputs, identify the affected field and permitted values, and retain the last valid saved state.

5. **FR-05 — Catalog import (Core).** The system shall import recipes through catalog adapters with unique IDs, names, instructions, servings, cooking times, ingredients/quantities/units, preference attributes/tags, dietary/allergen metadata, per-serving calories/protein, EUR ingredient unit prices, and source/license attribution metadata, reporting records with missing required fields or invalid values. Missing ingredient prices shall be reported without preventing recipe browsing, but recipes with incomplete pricing shall not claim budget compliance or a complete cost total.

6. **FR-06 — Hard filtering (Core).** The system shall exclude recipes violating saved dietary exclusions or the entered cooking-time maximum before selecting questions, showing dishes, or generating plans. Missing or contradictory metadata needed to verify an exclusion shall make the recipe ineligible; preferences, gamification, and evaluator output shall not override exclusions.

7. **FR-07 — Recipe presentation (Core).** The system shall show each recommended dish's name, cooking time, per-serving nutrition, estimated per-serving cost (or a missing-price notice), health evaluation, and required source attribution, with ingredients and instructions available in its details. When an asynchronous LLM evaluator is enabled, the dish card shall render immediately with a loading indicator or rule-based baseline and update once the LLM result arrives.

8. **FR-08 — Session decisions (Core).** The system shall record coarse-question answers and recipe likes/dislikes in the current user's session; right means yes/like and left means no/dislike. A decided recipe shall not reappear as undecided in that session.

9. **FR-09 — No remaining choices (Core).** The system shall explain when no candidates or undecided dishes remain and offer answer revision, explicit constraint changes, or a new session, without silently relaxing exclusions or repeating dishes.

10. **FR-10 — Changed inputs (Core).** The system shall recompute the candidate pool and affected evaluations after a coarse-question answer, dietary exclusion, profile nutritional goal, or planning input changes, before presenting the next question/dish or generating a plan. Saved plans shall retain their original settings.

11. **FR-11 — Adaptive preference discovery (Core).** The system shall offer coarse questions before dish swiping, selecting unanswered catalog attributes that distinguish the remaining candidates. Yes shall retain matching recipes and no shall retain non-matching recipes (recipes missing the queried attribute shall not count as matches). The user shall be able to revise answers or switch to dishes at any time. When no distinguishing question remains, the system shall offer the remaining dishes or the FR-09 explanation.

12. **FR-12 — Rule-based health evaluation (Core).** The system shall compare per-serving calories/protein with each active per-meal target (entered plan target divided by X, or saved profile per-meal target when no plan target is entered) and display actual values, comparison targets, and an explanation. It shall classify values with relative deviation <= 10% as within target and others as below/above target; without active targets it shall display nutrition without claiming target fit.

13. **FR-13 — LLM evaluation (Extension).** The system shall support an LLM evaluator sharing the health-evaluator interface and UI result fields to produce personalized explanations, without allowing it to change catalog nutrition, deterministic FR-12 classifications, hard constraints, or plan arithmetic.

14. **FR-14 — AI fallback (Extension, required with FR-13).** The system shall return a rule-based evaluation on timeout, unavailability, rejected requests, or invalid LLM results, identify its source, and allow discovery/planning to continue.

15. **FR-15 — Meal-plan generation (Core).** The system shall generate exactly X distinct currently eligible liked recipes from the current preference pool, with S servings, enforcing any entered EUR budget (excluding recipes with missing prices when a budget is active). Core generation shall work without an LLM.

16. **FR-16 — Plan ranking (Core).** The system shall rank evaluated compliant plans by: (1) lowest mean relative absolute deviation from active nutrient targets (zero if none), (2) lowest estimated total plan cost, (3) fewest occurrences of recipes from the user's saved plan history, and (4) lowest total cooking time, with ties broken by ascending lexicographic comparison of sorted recipe-ID lists. Hard constraints (dietary exclusions, cooking-time maximum, and active budget) shall take precedence over ranking.

17. **FR-17 — Unfulfilled plan request (Core).** The system shall identify the detected shortfall or conflicting constraints when no compliant X-meal plan is found and offer further discovery or explicit input changes. A bounded search failure shall not be presented as proof that no compliant plan exists.

18. **FR-18 — Plan summary (Core).** The system shall display selected recipes, servings, cooking times, per-person calorie/protein totals across the plan, and the estimated total plan cost (or a missing-price notice).

19. **FR-19 — Target deviations (Core).** The system shall show each active nutrient target's actual plan total, deviation, and whether it is within <= 10% of the target; unmet nutritional goals shall not silently relax dietary, time, or budget constraints.

20. **FR-20 — Shopping-list aggregation (Core).** The system shall derive an ingredient list from the plan, scale each recipe's quantities by S divided by its original servings, and combine identical ingredients with the same unit (including countable units) or compatible metric units (g/kg, ml/l), displaying names, total quantities, and units.

21. **FR-21 — Shopping-list pricing (Core).** The system shall calculate ingredient costs using one snapshot of catalog EUR unit prices, round each aggregated line half-up to cents, and sum the lines for shopping-list total, plan cost, and budget checks. It shall label costs as estimates and report missing prices without displaying a complete total or claiming budget compliance.

22. **FR-22 — Plan history (Core).** The system shall save generated plans (including their shopping list and price snapshot) on user request and use that user's saved recipe history for variety ranking under FR-16.

23. **FR-23 — Reopen plans (Core).** The system shall reopen the authenticated user's saved plans with their original recipes, servings, settings, nutrition, shopping list, and price snapshot.

24. **FR-24 — Gamification (Extension).** The system shall track optional user engagement progress—such as planning streaks, distinct dishes tried, and goal-adherence milestones—derived from saved plans, display progress on user request, and allow disabling gamification without affecting core planning or overriding hard constraints.
