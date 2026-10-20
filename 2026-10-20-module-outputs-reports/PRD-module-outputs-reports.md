# PRD: Module Outputs in Reports & Analytics

**Prototype:** Pending — Reports & Analytics screens being built
**Product:** Frinks Edge
**Status:** Draft
**Date:** 20 October 2026
**Owner:** Tanay Deo
**Related:** FP-528 (open epic: Analytics & Reports menu). Earlier Reports work: FP-3715 (Raw Images column, Download popup), FP-3622 (Cycle Complete column), FP-3727 (Write to DB custom key)
**Code checked:** 20 October 2026 — 34 repos at their latest default-branch commit (list in Appendix B)

---

## 1. Summary
(pending)

## 2. Why we are making this
- **Today:** Reports & Analytics shows only Pass or Fail for each cycle and each capture. To see why a capture failed (how many screws were found, which class a slot got, what serial number or code was read), someone has to open View JSON. It shows raw ids, one cycle at a time. The Class Name and Class Count columns show object counts only, show nothing for image classification, and never show read text or codes. The only workaround is one Write to DB rule per value. That gives one value per cycle (not per capture), and only for cycles run after the rule was made. Quality engineers, plant admins and support teams are affected.
- **Evidence:** customer request, and parity with competing inspection systems that show tool results next to each part.
- **After:** someone sets up once, per instance, which module values to show. A "Module Outputs" column then shows them as readable pairs, such as "Screw count: 4" or "Serial No.: SN-48213", for each capture of every cycle, on screen and in the Excel export.

## 3. Goals and non-goals
**Goals**
- G1. Inside Reports & Analytics, choose which values each capture's modules show, and what each value is called.
- G2. Show the chosen values as name: value pairs in a new "Module Outputs" column, per capture, for every cycle.
- G3. Cover every module type that reports a value.
- G4. The column works with the existing Search box and appears in the Excel export.

**Non-goals**
- New search or filter controls for module values. The existing Search box is enough.
- Fast Mode lines (Minute-wise), which store no per-capture results.
- Any change to how OK or NG is decided, or to PLC and dashboard keys.

## 4. Key concepts
- **Module output:** one value a module reports for a capture, shown as a pair such as "Screw count: 4".
- **Module Outputs setup:** the per-instance choice of which module outputs show in Reports, and the name each one shows under.

## 5. Impact map
(pending)

## 6. User stories and acceptance criteria
(pending)

## 7. Out of scope
(pending)

## 8. Open questions
(pending)

## 9. How we'll know it worked
(pending)

## Appendix A — Coverage
(pending)

## Appendix B — Code references
(pending)

## Changelog
- 20 October 2026 — Draft started (Tanay Deo)

## Checked, not affected
(pending)
