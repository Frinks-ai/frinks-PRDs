# PRD: Image Filter module

**Prototype:** Pending — the screens are built once the setup decisions are made. Earlier Print-OCR prototype with the filter list: [Print-OCR, Vision and Edge](https://vbv-fr.github.io/print-ocr/)
**Product:** Vision + Edge
**Status:** Draft
**Date:** 20 October 2026
**Owner:** Tanay Deo
**Related:** FP-2436 (OCR epic), FP-2375 (Masking module epic: the same "change the image, pass it on" pattern)
**Code checked:** 20 October 2026 — 34 repos at their latest default-branch commit (list in Appendix B)

---

## 1. Summary
*Draft. Grows as decisions are made.*
- A new module, **Image Filter**, can be added to a capture's recipe tree in Vision.
- It changes the image it gets from the module above it and passes the changed image to the modules placed under it, for example OCR.
- It runs an ordered chain of filters, one after another. There is no limit on how many filters a chain holds.
- It has no AI model, and it never makes a part OK or NG by itself.
- The filter list starts from the 26 filters agreed for the Print-OCR prototype: Binarize, Bot Hat, Clip, Close, Dilate, Edge Magnitude, Equalize, Erode, Fill Dark Holes, Fill Light Holes, Gradient Full, Gradient Horizontal, Gradient Vertical, Greyscale Distance, High Pass, Invert, Local Median, Local Median Normalization, Low Pass, Max (Top Hat/Bottom Hat), Open, Optical Density, Sharpen, Stretch, Threshold Range, Top Hat.

## 2. Why we are making this
- **Today:** Every module works on the camera image of its search area exactly as captured. Nothing can clean the image up first. Faint, noisy, shiny or low-contrast print reaches the OCR model as it is, which leads to misreads and false NGs. The only image clean-up in the inspection engine is hidden inside the Code Reader, and nobody can set it.
- **Evidence:** Competitor parity (competing read tools offer image-enhancement filters before reading), and the need to improve our OCR performance.
- **After:** An integrator places an Image Filter between a search area and the OCR (or another module), so that module works on a cleaner image.

## 3. Goals and non-goals
**Goals**
- G1. Better OCR reads on hard print, by cleaning the image before it is read.
- G2. Offer the image-enhancement filters that competing read tools offer.

**Non-goals**
- Pending — set during the decision rounds.

## 4. Key concepts
- **Image Filter module:** a module with no AI model that changes the image and passes it to the modules under it.
- **Filter chain:** the ordered list of filters inside one Image Filter module. Each filter works on the result of the one before it.
- **Filtered area** (working name): what the Image Filter hands down. It has the same size and position as its parent's area, with the pixels changed.

## 5. Impact map
*Draft, awaiting the PM's confirmation. User stories are linked once written.*

| Part | What will happen | User stories |
|---|---|---|
| Recipe tree (vision-recipe-tree) | "Image Filter" appears in Select Module on the module canvas, with rules for which modules may sit above and below it | — |
| Search Area (mod-search-area) | The usual parent of an Image Filter; the filtered area becomes something the modules below can pick as their input | — |
| OCR (mod-ocr) | OCR's "Base search area" list offers the filtered area, and OCR reads the filtered image | — |
| Result module (mod-result) | Modules under an Image Filter still count toward the part's OK/NG; the Image Filter itself adds no OK/NG | — |
| Python engine (engine-workflow) | Runs the filter chain on every image and hands the filtered image to the modules below | — |
| Edit Module Configurations (edge-emc) | A new Image Filter section where the chain and each filter's values are tuned, with the image before and after | — |
| Instance creation (vision-instance-creation) | The instance package carries the Image Filter setup | — |
| Instance upload (edge-instance-details) | Edge stores the Image Filter setup when an instance is uploaded | — |
| Model training (vision-model-training) | Decide whether a model under an Image Filter is trained on filtered images, as it will see them on the line | — |
| Edge upgrades (edge-release-upgrade) | An Edge version that does not know the Image Filter must not take down the whole inspection when it receives one | — |
| Health and error codes (edge-health) | New error codes for Image Filter failures | — |

Under review (may change): the other modules that could sit under an Image Filter (ODC, Classification, Segmentation, Code Reader, Colour Detection, Shape Extractor, Bead Inspection, Orientation, Matting, Tracker), Masking and the Master Image as neighbours, annotation, auto-annotation, the Data Recommender, model evaluation, image saving, the Live Dashboard, Reports, analysed-image settings, Edge backup and migration, the Python Docker image, and Vision and Edge roles.

## 6. User stories and acceptance criteria
Pending — written after the decision rounds, from the coverage sheet.

## 7. Out of scope
- Pending.

## 8. Open questions
| # | Question | Owner | Needed by |
|---|---|---|---|
| Q1 | When an AI model (for example OCR) sits under an Image Filter, are its training images filtered the same way as on the line? | Tanay Deo | Before user stories |
| Q2 | Which modules may sit under an Image Filter, and which may sit above it? | Tanay Deo | Before user stories |
| Q3 | Where are the chain and the filter values set: in Vision, on Edge in Edit Module Configurations, or both? | Tanay Deo | Before user stories |
| Q4 | Is the filtered image shown on the Live Dashboard, saved to disk and shown in Reports? | Tanay Deo | Before user stories |

## 9. How we'll know it worked
| Measure | Target |
|---|---|
| Pending | Pending |

## Appendix A — Coverage
Pending — the coverage check runs on the final draft.

## Appendix B — Code references
- **Code Reader (only image clean-up in the engine today):** `vip-inference-python`: `scripts/assembly/interfaces/utils/codeReader.py:48-51` (CLAHE), `:80-88` (adaptive threshold), `:162-173` (Otsu threshold) — internal retry steps, not user-settable.
- **OCR module (no image filter today):** Vision `ocr-module/` panel, Vision `ocr_config` table, Edge `OCRSection.jsx`, engine `ocrModule.py` — none hold a filter setting.

Repos checked: assembly-dataset-recommender @ 0556f06 (2026-07-21), Assembly-model-evaluation @ 7a0403a (2025-11-04), assembly-training @ 725f834 (2026-06-02), Assembly-Training-OCR @ 99d0d62 (2025-07-08), Auto-annotation @ d213b71 (2025-10-30), auto-training-backend @ 63ba9a6 (2026-08-04), Classification-Evaluation @ eda0d3b (2025-12-19), classification-training-docker @ 3f7a64e (2025-11-05), Craft-model-evaluation @ 5a95e16 (2025-07-10), frinkschat-backend @ 88e5c91 (2026-07-21), frinksedge-deployment-backend @ 009fc11 (2026-10-19), frinksedge-deployment-frontend @ 7e92de34 (2026-10-19), frinksedge-file-releases @ 7041254 (2026-07-16), frinksedge-health @ e8ef112 (2026-10-15), frinksedge-inferencing-backend @ eddd8e5 (2026-10-19), frinksedge-installer-electron @ b4a5049 (2026-10-12), frinksedge-plc-backend @ 254d08e (2026-10-15), frinksedge-report @ 5fe5390 (2026-10-15), frinksedge-report-frontend @ 9893a19 (2026-10-13), frinksedge-staging-environment @ 4993036 (2026-09-09), frinksvision-backend @ eda944f (2026-10-14), frinksvision-frontend @ 110a23c (2026-10-19), OCR-Evaluation @ c9c413c (2025-07-10), OCR-Training-Detection-ALL @ fc59f48 (2026-10-14), Python-inference-docker @ 62e22bd (2026-05-29), Recommendation-classification @ 0796dbb (2026-08-03), Recommendation-OCR @ ae1e953 (2026-04-30), release-manager-backend @ b8016f7 (2026-09-22), Segmentation-Data-Recommender @ d6f679e (2026-07-22), Segmentation-Evaluation @ 44bb8c6 (2025-08-28), Segmentation-Training @ f749aab (2025-08-22), train-utils @ d3e6378 (2026-09-15), vip-inference-python @ 93451d1 (2026-10-15), workflow-dataprocessor @ d2cd9bd (2026-08-11)

## Changelog
- 20 Oct 2026 — Draft started as "Image filters for OCR"; scope changed to a standalone Image Filter module that any module can sit under (Tanay Deo)

## Checked, not affected
*Draft.*
- vision-captures-cameras, vision-projects, vision-variants: the Image Filter needs no class type and no variant logic.
- edge-camera-capture, edge-deployment, edge-networking, edge-system-time, edge-installer: nothing new reaches them; the setup travels through the engine config.
- edge-model-loading-variant-change, edge-encryption-keys: the Image Filter has no model to load, swap or decrypt.
- edge-actions, edge-plc-read, edge-plc-write, edge-plc-setup, edge-internal-actions, edge-dashboard-builder: the Image Filter gives no result or key.
