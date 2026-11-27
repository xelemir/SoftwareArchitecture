# MealSwipe — Non-Functional Requirements (Task 8)

## Quality Model Selection and Justification (ISO/IEC 25010:2011)

Based on the ISO/IEC 25010:2011 product quality model, the MealSwipe PIBA, and the prioritized stakeholder concerns, the following quality characteristics are most relevant to this project:

- **Performance efficiency (High priority):** Swipe-based meal discovery must feel immediate (`< 200 ms` typical card transitions) and meal-plan generation must complete within seconds for concurrent users; otherwise users revert to manual planning (PIBA responsiveness; end users).
- **Reliability (High priority):** Core planning must remain available when the external LLM fails via automatic rule-based fallback, preserve user profiles and saved plans across restarts, and enforce dietary exclusions without failure (PIBA availability; all user groups, especially users with dietary restrictions).
- **Security (High priority):** Personal dietary exclusions, nutritional goals, and plan history must be isolated per authenticated user, protected in transit, and kept out of external LLM requests (PIBA privacy; health-conscious users and users with dietary restrictions).
- **Functional suitability (High priority):** Accurate calorie/protein totals, exact `<= 10%` target classifications, cent-accurate EUR cost estimates, and zero allergen/exclusion violations are essential for user trust (health-conscious, budget-conscious, and dietary-restricted users).
- **Maintainability (High priority):** The modular monolith with ports and adapters requires swapping health evaluators (rule-based vs. LLM) and recipe catalog sources with minimal localized code changes (PIBA modifiability; software developers, project manager).
- **Usability (High priority):** Reducing planning effort requires first-time users to generate a meal plan and priced shopping list quickly without training, and accessible keyboard/pointer alternatives to swipe gestures (PIBA reduced planning effort; end users).
- **Portability (Medium priority):** Reproducible containerized setup via Docker enables reliable evaluation, onboarding, and grading across developer machines without external service dependencies (software developers, project manager).
- **Compatibility (Medium priority):** Integrating interchangeable catalog sources and external LLM/recipe APIs requires uniform normalization and adherence to external provider licensing and usage terms (external service providers, software developers).

---

## Non-Functional Requirements

1. **NFR-01 — Discovery responsiveness (Core; performance efficiency).** The system shall render the next question or eligible dish in **less than 200 ms for at least 95% of transitions**, with **every transition completing within 500 ms**, after a yes/no or like/dislike action and without waiting for an LLM (asynchronous LLM evaluations update the already-rendered card per FR-07).
   **Context:** Warmed deployment, 2 CPU cores, 4 GiB RAM, 100 recipes, 10 concurrent users, 20 ms network round-trip latency and 10 Mbit/s bandwidth; a next question/dish exists. **Verification:** Browser input-to-render timing of 500 question and 500 dish transitions; at least 95% meet 200 ms, 100% meet 500 ms, and none fails.

2. **NFR-02 — Planning response (Core; performance efficiency).** The system shall display a complete meal plan with its priced shopping list or the FR-17 explanation within **2 seconds for at least 95 of 100 requests**, with every request finishing within **5 seconds**; all feasible test requests shall return a compliant meal plan.
   **Context:** NFR-01 environment, with feasible and infeasible requests for 1–7 meals and 1–4 servings. **Verification:** Measure browser submission-to-result times and check plan compliance on feasible cases; application errors or false failures on feasible cases fail acceptance.

3. **NFR-03 — Prototype capacity (Core; performance efficiency / reliability).** The system shall support **10 concurrent users for 15 minutes with zero unexpected server errors or lost acknowledged answers/swipes**, while meeting NFR-01 and NFR-02.
   **Context:** NFR-01 environment; each user submits one action every 2 seconds, with 80% discovery actions, 10% profile reads, and 10% plan requests; start new sessions on exhaustion. **Verification:** Load testing and comparison of acknowledged decisions with persisted session state.

4. **NFR-04 — AI-failure recovery (Extension; reliability).** The system shall stop waiting for an LLM after at most **3 seconds** and return a rule-based result within **4 seconds** of the evaluation request (FR-14); discovery and planning shall continue to meet NFR-01 and NFR-02.
   **Context:** LLM enabled; local application/database healthy. **Verification:** Inject timeouts, connection failures, rejected requests, and malformed responses ten times each; measure fallback and core response times.

5. **NFR-05 — Restart recovery (Core; reliability).** The system shall preserve **all acknowledged user accounts, profile updates, saved plans, and associated priced shopping lists with their price snapshots** through an application restart and make them retrievable within **60 seconds**.
   **Context:** Database and persistent storage remain intact. **Verification:** Compare ten saved profiles, plans, and shopping lists before and after ordinary restart and abrupt application termination.

6. **NFR-06 — LLM privacy (Extension; security).** Outgoing LLM evaluation requests shall contain only a fixed template and allowlisted anonymized numeric nutrients/comparison targets, with **zero user/session identifiers, dietary exclusions, preference answers, history, or user-authored text** in application-controlled payloads, URLs, or headers.
   **Context:** All evaluation requests, including retries and error paths. **Verification:** Capture requests to a stub provider and check allowed fields and planted synthetic identifiers.

7. **NFR-07 — User isolation (Core; security).** The system shall enforce authentication (FR-01) and permit **zero cross-user reads or writes** of accounts, profiles, question answers, swipe decisions, saved plans/history, shopping lists, or gamification progress in the isolation test suite.
   **Context:** Two distinct authenticated user contexts and unauthenticated requests. **Verification:** Exercise every resource read/write with another user's identifiers and missing/invalid credentials or session tokens; verify rejection and unchanged data.

8. **NFR-08 — Transport protection (Core when publicly deployed; security).** The system shall send credentials, profile, preference, plan, and shopping-list data over **HTTPS with zero plaintext sensitive requests or mixed-content loads**; external provider connections shall use HTTPS.
   **Context:** Public network deployment; browser localhost development is exempt. **Verification:** Inspect network traffic throughout the core flow and verify HTTP redirects before sensitive inputs are accepted.

9. **NFR-09 — Evaluator replacement (Core architecture; maintainability).** Selecting an existing evaluator shall require **zero web-client/planner source changes**; adding a contract-conforming local stub shall take a familiar developer at most **8 hours**, changing only evaluator code, registration/configuration, and its tests.
   **Context:** Existing codebase with documented evaluator interface. **Verification:** Timed replacement exercise, changed-file review, and common consumer/contract checks.

10. **NFR-10 — Catalog-source replacement (Core; maintainability / compatibility).** A familiar developer shall add a second synthetic source adapter within **8 hours**, without changing discovery, planning, evaluation, or client code; both sources shall pass the same FR-05 normalization and validation checks.
    **Context:** Equivalent recipes in different input formats, including ingredient price and attribution fields. **Verification:** Timed change exercise and comparison of normalized records, including invalid imports.

11. **NFR-11 — Learnability (Core; usability).** At least **4 of 5 first-time users** shall sign in, complete coarse-preference discovery, like enough dishes, and generate a seven-meal plan with its priced shopping list within **5 minutes**, without assistance beyond a standard task instruction.
    **Context:** Browser-familiar adults and sufficient compliant recipes with price data. **Verification:** Observe users and record completion times, errors, and requests for help.

12. **NFR-12 — Gesture alternatives (Core; usability).** **Every core action**, including signing in, editing the profile, answering/revising questions, switching to dishes, liking/disliking, generating a plan, and viewing the shopping list, shall work by keyboard and visible pointer/touch controls without requiring a swipe or horizontal scrolling.
    **Context:** Recorded stable Chrome/Firefox versions on desktop and mobile Safari; viewport widths 375 and 1,280 pixels; keyboard checks on desktop. **Verification:** Complete the flow separately by keyboard and pointer, including validation errors; check labels, visible focus, and absence of keyboard traps.

13. **NFR-13 — Calculation accuracy (Core; functional suitability).** Nutrition results shall match independent reference values within **0.1 kcal / 0.1 g protein before display rounding**, with exact FR-12/FR-19 (`<= 10%`) classifications. Pricing totals shall agree exactly to **EUR 0.01** under FR-20 and FR-21.
    **Context:** Fixed synthetic cases covering servings, target boundaries, unit conversion, ingredient merging, half-up rounding, and budget boundaries. **Verification:** Compare every case with independently calculated expected results.

14. **NFR-14 — Dietary integrity (Core; functional suitability / reliability).** The system shall show **zero recipes violating active dietary exclusions** across the adversarial fixture suite.
    **Context:** Declared catalog metadata; cover each supported exclusion, missing/contradictory data, conflicting answers, answer/profile changes, and adversarial LLM output when enabled. **Verification:** Independently check every recommended/planned recipe against FR-06, including empty-result handling.

15. **NFR-15 — Reproducible deployment (Core; portability).** A second developer shall build/start a clean checkout, load 100 mock recipes (including mock price data), and complete **sign-in → questions → dishes → meal plan and priced shopping list within 30 minutes**, using documented Docker commands without source changes or LLM credentials.
    **Context:** Host allocating 2 CPU cores and 4 GiB RAM, documented Docker version installed, network available for build dependencies. **Verification:** Timed clean-install exercise and completion of the core flow.

16. **NFR-16 — External provider compliance (Core; compatibility / functional suitability).** The system shall retain and display required source/license attribution for imported catalog recipes (FR-05, FR-07) and enforce configured outbound API rate limits for external recipe or LLM providers, with **zero unattributed licensed records and zero rate-limit violations** during adapter and integration tests.
    **Context:** Catalog imports containing licensed records and configured provider rate limits against stub external endpoints. **Verification:** Inspect rendered recipe cards/details for required attribution and run burst tests against stub provider endpoints to confirm rate-limit enforcement.
