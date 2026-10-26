# PRD: Image Enhancement under Masking

**Prototype:** Pending — this PRD has not been started
**Product:** Vision
**Status:** Draft
**Date:** 26 October 2026
**Owner:** Tanay Deo
**Related:** [Image Enhancement module PRD](../2026-10-20-image-enhancement-module/PRD-image-enhancement-module.md) (split out of its discussion point Q1 on 26 Oct 2026)
**Code checked:** Not yet

---

## Discussion points
| # | Point to settle | Options on the table | Owner | Needed by |
|---|---|---|---|---|
| Q1 | An integrator builds Search Area → Masking (blanks out a logo) → Image Enhancement → OCR and clicks Train. Today the trainers of several model types (OCR reading, object detection, classification) stop with an error whenever a Masking module sits anywhere above the module being trained, so this tree could not be trained. The part's OK/NG is not affected: the enhanced area counts toward the verdict even under Masking. | (a) Allow Image Enhancement under Masking and make those trainers handle Masking. (b) Keep it greyed out under Masking until training under Masking is fixed. | Tanay Deo, Smit | When this PRD is picked up |

## 1. Summary
Not started. Until this PRD lands, Select Module greys out Image Enhancement under a Masking module (Image Enhancement module PRD, AC1.2).
