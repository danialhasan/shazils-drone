# Decision Log

## 2025-12-27 — Switch to AliExpress Foam Glider & Return Pi Zero 2 W
- **Context:** Earlier docs focused on converting the Reptile S800 V2 and assumed a Raspberry Pi Zero 2 W + Coral TPU payload.
- **Issue:** The Pi Zero 2 W was returned (availability/power constraints) and the S800 kit is no longer part of the plan. The repo accidentally reverted to the old S800/Pi version after syncing with GitHub.
- **Decision:** Reframe the entire project around the AliExpress foam glider and mark compute hardware as TBD until a new SBC is selected. This keeps documentation aligned with actual hardware and avoids referencing returned gear.
- **Actions:**
  - Updated README + all docs (parts list, phases, wiring) to reference the foam glider and TBD compute payload.
  - Noted explicitly that the Pi Zero 2 W was returned so future contributors don’t reintroduce it by mistake.
  - Added this decision log to capture the rationale; future merges should preserve these sections.
- **Follow-up:** When a new SBC is purchased, update Phase 3 docs with concrete instructions and record the choice here.
- **Current hardware status (2025-12-27):** Motor, propeller, pushrods, and control horns are already on hand; the servos will be reordered to suit the new foam glider build.
