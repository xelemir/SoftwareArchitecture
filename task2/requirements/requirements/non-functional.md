# MealSwipe — Non-Functional Requirements (Task 8)

1. **NFR-01 — Discovery responsiveness (Core; performance efficiency).** The system shall render the next question or eligible dish in **less than 200 ms** after a yes/no or like/dislike action, without waiting for an LLM.
   **Context:** Warmed deployment, 2 CPU cores, 4 GiB RAM, 100 recipes, 10 concurrent users, 20 ms network round-trip latency and 10 Mbit/s bandwidth; a next question/dish exists. **Verification:** Browser input-to-render timing of 500 question and 500 dish transitions; every transition meets the limit and none fails.

2. **NFR-02 — Planning response (Core; performance efficiency).** The system shall display a complete meal plan or the FR-16 explanation within **2 seconds for at least 95 of 100 requests**, with every request finishing within 5 seconds.
   **Context:** NFR-01 environment, with feasible and infeasible requests for 1–7 meals and 1–4 servings. **Verification:** Measure browser submission-to-result times; application errors fail acceptance.

3. **NFR-03 — Prototype capacity (Core; performance efficiency/reliability).** The system shall support **10 concurrent users for 15 minutes with zero unexpected server errors or lost acknowledged answers/swipes**, while meeting NFR-01/02.
   **Context:** NFR-01 environment; each user submits one action every 2 seconds, with 80% discovery actions, 10% profile reads, and 10% plan requests; start new sessions on exhaustion. **Verification:** Load testing and comparison of acknowledged decisions with session state.

4. **NFR-04 — AI-failure recovery (Extension; reliability).** The system shall stop waiting for an LLM after at most **3 seconds** and return a rule-based result within **4 seconds** of the evaluation request; discovery and planning shall continue to meet NFR-01/02.
   **Context:** LLM enabled; local application/database healthy. **Verification:** Inject timeouts, connection failures, rejected requests, and malformed responses ten times each; measure fallback and core response times.

5. **NFR-05 — Restart recovery (Core; reliability).** The system shall preserve **all acknowledged profile updates and saved plans** through an application restart and make them retrievable within **60 seconds**; saved shopping lists shall retain their price snapshots when that extension is used.
   **Context:** Database and persistent storage remain intact. **Verification:** Compare ten saved profiles/plans before and after ordinary restart and abrupt application termination.

6. **NFR-06 — LLM privacy (Extension; security).** Outgoing LLM evaluation requests shall contain only a fixed template and allowlisted numeric nutrients/comparison targets, with **zero user/session identifiers, dietary exclusions, preference answers, history, or user-authored text** in application-controlled payloads, URLs, or headers.
   **Context:** All evaluation requests, including retries and error paths. **Verification:** Capture requests to a stub provider and check allowed fields and planted synthetic identifiers.

7. **NFR-07 — User isolation (Core; security).** The system shall permit **zero cross-user reads or writes** of profiles, question answers, swipe decisions, saved plans/history, or optional shopping lists in the isolation test suite.
   **Context:** Two distinct user contexts. **Verification:** Exercise every resource read/write with another user's identifiers and missing/invalid user context; verify rejection and unchanged data.

8. **NFR-08 — Transport protection (Core when publicly deployed; security).** The system shall send profile, preference, and plan data over **HTTPS with zero plaintext sensitive requests or mixed-content loads**; provider connections shall use HTTPS.
   **Context:** Public network deployment; browser localhost development is exempt. **Verification:** Inspect network traffic throughout the core flow and verify HTTP redirects before sensitive inputs are accepted.

9. **NFR-09 — Evaluator replacement (Core architecture; maintainability).** Selecting an existing evaluator shall require **zero web-client/planner source changes**; adding a contract-conforming local stub shall take a familiar developer at most **8 hours**, changing only evaluator code, registration/configuration, and its tests.
   **Context:** Existing codebase with documented evaluator interface. **Verification:** Timed replacement exercise, changed-file review, and common consumer/contract checks.

10. **NFR-10 — Catalog-source replacement (Core; maintainability/compatibility).** A familiar developer shall add a second synthetic source adapter within **8 hours**, without changing discovery, planning, evaluation, or client code; both sources shall pass the same normalization/validation checks.
    **Context:** Equivalent recipes in different input formats; price fields optional. **Verification:** Timed change exercise and comparison of normalized records, including invalid imports.

11. **NFR-11 — Learnability (Core; interaction capability).** At least **4 of 5 first-time users** shall complete coarse-preference discovery, like enough dishes, and generate a seven-meal plan within **5 minutes**, without assistance beyond a standard task instruction.
    **Context:** Browser-familiar adults and sufficient compliant recipes; no shopping/pricing requirement. **Verification:** Observe users and record completion times, errors, and requests for help.

12. **NFR-12 — Gesture alternatives (Core; interaction capability).** **Every core action**, including answering/revising questions, switching to dishes, liking/disliking, and generating a plan, shall work by keyboard and visible pointer/touch controls without requiring a swipe or horizontal scrolling.
    **Context:** Recorded stable Chrome/Firefox versions on desktop and mobile Safari; viewport widths 375 and 1,280 pixels; keyboard checks on desktop. **Verification:** Complete the flow separately by keyboard and pointer, including validation errors; check labels, visible focus, and absence of keyboard traps.

13. **NFR-13 — Calculation accuracy (Core; pricing checks Extension; functional suitability).** Nutrition results shall match independent reference values within **0.1 kcal/0.1 g protein before display rounding**, with exact FR-11/18 classifications. Pricing totals shall agree exactly to **EUR 0.01** under FR-19/20.
    **Context:** Fixed synthetic cases covering servings and target boundaries; extension cases include unit conversion, ingredient merging, rounding, and budget boundaries. **Verification:** Compare every case with independently calculated expected results.

14. **NFR-14 — Dietary integrity (Core; safety).** The system shall show **zero recipes violating active dietary exclusions** across the adversarial fixture suite.
    **Context:** Declared catalog metadata; cover each supported exclusion, missing/contradictory data, conflicting answers, answer/profile changes, and adversarial LLM output when enabled. **Verification:** Independently check every recommended/planned recipe against FR-05, including empty-result handling.

15. **NFR-15 — Reproducible deployment (Core; flexibility).** A second developer shall build/start a clean checkout, load 100 mock recipes, and complete **questions → dishes → plan within 30 minutes**, using documented Docker commands without source changes, price data, or LLM credentials.
    **Context:** Host allocating 2 CPU cores and 4 GiB RAM, documented Docker version installed, network available for build dependencies. **Verification:** Timed clean-install exercise and completion of the core flow.
