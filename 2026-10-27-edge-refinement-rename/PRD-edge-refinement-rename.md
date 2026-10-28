# PRD: Rename Matting to Edge Refinement — Phase 1

**Prototype:** None — no new screens or layouts; only names and wording change (PM decision, 27 Oct 2026)
**Product:** Vision + Edge
**Status:** In review
**Date:** 28 October 2026
**Owner:** Swayam (SDE), Shreyas (AI)
**Related:** Original module build: FP-2454 (epic, now assigned to Deb) with FP-2343, FP-2349, FP-2352, FP-2356 and stories FP-2344 to FP-2363. Same principle: FP-3473 (model names removed from logs and screens). Full place-by-place list: matting-exposure-audit.xlsx (shared separately).
**Code checked:** 27 October 2026 — 34 repos at their latest default-branch commit, plus the live branches of Auto-annotation, Segmentation-Training and Segmentation-Evaluation (list in Appendix B)

---

## Discussion points
None.

## 1. Summary
- Phase 1 hides the word "Matting" everywhere a person can see it: Vision and Edge screens, the browser's inspect tools (Network, Console, Sources) and error logs on Edge and in Vision cloud jobs. Everything there says **Edge Refinement**.
- New modules of this type are named **ER_1, ER_2, …** in Vision, and their Edge keys use **ER**.
- The module-type entry in the Vision and Edge databases becomes **edgeRefinement / Edge Refinement**, so the browser only ever receives the new name.
- Inside the servers and Python jobs nothing else is renamed in phase 1: the module's settings table, Python code, setting names and file names keep "matting" until phase 2.
- The module works exactly as before: same refined masks, same OK/NG, same place in the recipe tree.

## 2. Why we are making this
- **Today:** the module shows its research name, "Matting", on the add-module list, panel title, recipe filters, auto-annotation settings, error pop-ups and Edge key names (MAT), in the data the browser receives, and in logs. About 700 places in 20 repos use it.
- **Evidence:** product naming decision: the product never shows the academic name of a technique, the same principle as the model-name clean-up in FP-3473.
- **After:** nothing a user, integrator or support engineer can see, including in the browser's inspect tools and in logs, says "Matting". The deeper rename follows in phase 2.

## 3. Goals and non-goals
**Goals**
- G1. No Vision or Edge screen, key or report says "Matting"; they say "Edge Refinement" or ER.
- G2. The browser's inspect tools (Network, Console, Sources) never show "matting" on any Vision or Edge screen.
- G3. No error log on Edge or in Vision cloud jobs says "matting".
- G4. The module and everything around it keeps working exactly as before.

**Non-goals**
- Changing what the module does, its settings or its place in the recipe tree.
- Phase 2 work (see Out of scope).

## 4. Key concepts
- **Edge Refinement:** the new name of the Matting module. It takes a Segmentation module's masks and sharpens their edges.
- **ER:** the new name prefix for these modules (ER_1) and in their Edge key names. It replaces MAT.
- **edgeRefinement:** the new name of the module-type entry, the name the browser receives. It replaces matting.
- **Phase 1 / Phase 2:** phase 1 (this PRD) hides the word from everything people see; phase 2 (later, own PRD) renames what only the code and databases hold.

## 5. Impact map
The parts this changes. Parts checked and not affected are listed at the very end.

| Part | What will happen | User stories |
|---|---|---|
| Matting (mod-matting) | Shown as Edge Refinement everywhere people look; works the same | US1, US6, US10 |
| Recipe editor (vision-recipe-tree) | Add-module list, panel title, tooltip, pop-ups, server messages and new module names say Edge Refinement / ER; parent rules unchanged; module-type entry renamed | US1, US6, US9, US12 |
| Captures list (vision-captures-cameras) | Recipe Management filter option and module summary say Edge Refinement | US2 |
| Auto-annotation (vision-auto-annotation) | Toggle label says Edge Refinement; the browser never sees the old setting name; job logs reworded; toggle works as before | US3, US5, US6, US9, US12 |
| Datasets and annotations (vision-datasets-annotations) | A capture with the module still goes through the sample-data steps | US9 |
| Instance creation (vision-instance-creation) | Packages carry the renamed module-type entry | US12 |
| Model training (vision-model-training) | Job logs reworded; jobs run as before | US5, US9 |
| Model evaluation (vision-model-evaluation) | Job logs reworded; jobs run as before | US5, US9 |
| Data Recommender (vision-training-sets-recommender) | Job logs reworded; runs as before | US5, US9 |
| Access (vision-access) | No role or permission change | US8 |
| Masking (mod-masking) | Still accepts Edge Refinement as its parent and gets refined masks | US9, US10 |
| Orientation & Tilt Check (mod-orientation) | Still accepts Edge Refinement as its parent and gets refined masks | US9, US10 |
| Shape Extractor (mod-shape-extractor) | Still accepts Edge Refinement as its parent and gets refined masks | US9, US10 |
| Result (mod-result) | Same pass/fail for the module | US10 |
| AI engine (engine-workflow) | Log lines reworded; runs as before | US5, US10 |
| AI engine images (engine-runtime-packaging) | No image change | US11 |
| Instance upload (edge-instance-details) | Accepts packages with the renamed entry; Instance Details shows no old name | US6, US12 |
| Deployment (edge-deployment) | Messages reworded; screens and their data show no old name | US5, US6 |
| Inferencing (edge-inferencing-customization) | Messages reworded; result data sent to screens shows no old name | US5, US10 |
| Actions (edge-actions) | Key names use ER | US4 |
| PLC Read (edge-plc-read) | Key names use ER | US4 |
| PLC Write (edge-plc-write) | Key names use ER | US4 |
| Internal Actions (edge-internal-actions) | Key names use ER | US4 |
| Dashboard Builder (edge-dashboard-builder) | Key names use ER | US4 |
| Reports (edge-reports-analytics) | Raw result data names the module edgeRefinement | US4 |
| Health (edge-health) | Model-load error detail says Edge Refinement; no new error codes | US5, US11 |
| Model loading (edge-model-loading-variant-change) | Loads the refinement model as before | US10 |
| Backup and migration (edge-backup-migration) | Edge upgrade renames the module-type entry | US12 |
| Releases (edge-release-upgrade) | Scripts bundle changes; Vision, Edge and Python ship together | US11, US12 |

## 6. User stories and acceptance criteria

### US1 — The recipe editor says Edge Refinement
*As a project member building a recipe, I want the module to be called Edge Refinement, so that I never see the technique's research name.*
- **Kind:** Feature
- **Parts:** vision-recipe-tree, mod-matting
- **Teams:** SDE
- **Covers:** C4, C18
- **Today:** the add-module list, panel title, tooltip and pop-ups say "Matting", and new modules are named MAT_1.

- **AC1.1** Clicking + under a Segmentation module lists the module as "Edge Refinement".
- **AC1.2** A newly added module of this type is named ER_1, the next one ER_2; the ER part is locked like other module prefixes.
- **AC1.3** The module's settings panel title reads "Edge Refinement".
- **AC1.4** The Module Inputs info icon reads "The search area whose region this Edge Refinement module will process."
- **AC1.5** Choosing the wrong number of class instances shows "Please select 1-2 class instances for Segmentation/Edge Refinement input."
- **AC1.6** Placing a module under a parent it does not allow shows messages that say "Edge Refinement", e.g. "The parent module must be a segmentation for an Edge Refinement module."

### US2 — Recipe Management says Edge Refinement
*As a project member, I want the captures list to use the new name, so that filters and summaries match the recipe editor.*
- **Kind:** Feature
- **Parts:** vision-captures-cameras
- **Teams:** SDE
- **Covers:** C18
- **Today:** the module filter offers "Matting" and a recipe's module summary lists "Matting".

- **AC2.1** The module filter on Recipe Management offers "Edge Refinement".
- **AC2.2** A recipe's module summary on Recipe Management lists "Edge Refinement".

### US3 — The auto-annotation toggle says Edge Refinement
*As an annotator, I want the edge-sharpening toggle in auto-annotation to use the new name, so that it matches the module.*
- **Kind:** Feature
- **Parts:** vision-auto-annotation
- **Teams:** SDE
- **Covers:** C14
- **Today:** the Segmentation settings in Configure Auto Annotation show a toggle labelled "Matting".

- **AC3.1** The toggle in the Segmentation settings of Configure Auto Annotation is labelled "Edge Refinement".

### US4 — Edge keys and reports use the new name
*As an integrator, I want this module's keys and report data to use ER and edgeRefinement, so that nothing on Edge shows the old name.*
- **Kind:** Feature
- **Parts:** edge-actions, edge-plc-read, edge-plc-write, edge-internal-actions, edge-dashboard-builder, edge-reports-analytics
- **Teams:** SDE
- **Covers:** C9, C12
- **Today:** this module's keys carry MAT as their module type, and the raw result data in Reports names the module "matting".

- **AC4.1** Keys created for this module carry ER as their module type wherever keys are listed: PLC Read Config, PLC Write Config, Internal Action Config and Dashboard Builder.
- **AC4.2** The raw result data of a cycle in Reports & Analytics names this module's type "edgeRefinement".

### US5 — Error logs say Edge Refinement
*As a Frinks support engineer, I want every log line and error detail about this module to use the new name, so that the old name never shows up on a plant machine or in a job log.*
- **Kind:** Feature
- **Parts:** engine-workflow, edge-inferencing-customization, edge-deployment, edge-health, vision-model-training, vision-model-evaluation, vision-training-sets-recommender, vision-auto-annotation
- **Teams:** SDE + AI
- **Stakeholders:** Swayam (SDE), Shreyas (AI)
- **Covers:** C13
- **Today:** logs, validation and database error messages and the model-load error detail say "matting".

- **AC5.1** Running a recipe with the module on Edge writes no log line containing "matting"; every line about it says "Edge Refinement".
- **AC5.2** Edge server messages about this module (result checks and database errors) say "Edge Refinement".
- **AC5.3** Training, evaluation, Data Recommender and auto-annotation jobs that use the module write no log line containing "matting".
- **AC5.4** When the refinement model fails to load, the error detail shown for MODELLOAD_005 in Health reads "Edge Refinement model loading".
- **AC5.5** (removed): setting names move to phase 2.
- **AC5.6** (removed): file and folder names move to phase 2.

### US6 — The browser's inspect tools never show the old name
*As a Frinks support engineer, I want the browser's inspect tools to show only the new name, so that anyone opening them on a Vision or Edge screen never sees "matting".*
- **Kind:** Feature
- **Parts:** mod-matting, vision-recipe-tree, vision-auto-annotation, edge-instance-details, edge-deployment
- **Teams:** SDE
- **Covers:** C7
- **Today:** the data Vision and Edge send to the browser names the module type "matting" and "Matting", saving auto-annotation settings sends "matting", and the Vision frontend code holds names like "matting-module".

- **AC6.1** On every Vision screen, no request or response in the inspect tool's Network tab contains "matting"; the module type arrives as edgeRefinement / Edge Refinement.
- **AC6.2** On every Edge screen (Deployments, Instance Details, Integrations, Dashboard Builder, Reports & Analytics, Health), no request or response in the Network tab contains "matting".
- **AC6.3** Using these screens with the module writes nothing containing "matting" to the inspect tool's Console.
- **AC6.4** The Vision and Edge frontend code shown in the inspect tool's Sources tab contains no "matting".

### US7 — (removed): chat assistant manuals move to phase 2.

### US8 — Rules
*As an Org Admin, I want access to stay as it is, so that the rename does not change who can do what.*
- **Kind:** Rules
- **Parts:** vision-access
- **Teams:** SDE
- **Covers:** C1

- **AC8.1** No role or permission changes: whoever could add and configure Matting can add and configure Edge Refinement, and nobody else.

### US9 — Keeps working: Vision and Python jobs
*As a project member, I want everything around the module in Vision and in Python jobs to behave as before, so that the rename changes nothing but names.*
- **Kind:** Keeps working
- **Parts:** vision-recipe-tree, mod-masking, mod-orientation, mod-shape-extractor, vision-datasets-annotations, vision-auto-annotation, vision-model-training, vision-model-evaluation, vision-training-sets-recommender
- **Teams:** SDE + AI
- **Stakeholders:** Swayam (SDE), Shreyas (AI)
- **Covers:** C4, C14

- **AC9.1** The module can still be added only under a Segmentation module, and Masking, Orientation & Tilt Check and Shape Extractor can still be added under it.
- **AC9.2** A capture whose recipe has the module still goes through the sample-data steps.
- **AC9.3** With the auto-annotation toggle on, segmentation auto-annotation still sharpens mask edges; with it off, it does not.
- **AC9.4** Training, evaluation and Data Recommender runs for a recipe with the module still fetch the refinement model and finish as before.

### US10 — Keeps working: Edge runtime
*As a quality engineer, I want the module to inspect exactly as before on Edge, so that results do not move because of a rename.*
- **Kind:** Keeps working
- **Parts:** mod-matting, mod-result, mod-masking, mod-orientation, mod-shape-extractor, engine-workflow, edge-inferencing-customization, edge-model-loading-variant-change
- **Teams:** AI
- **Covers:** C8, C9, C10

- **AC10.1** The same image gives the same refined masks and the same OK/NG for the module as before the release.
- **AC10.2** Masking, Orientation & Tilt Check and Shape Extractor placed under the module still receive its refined masks.
- **AC10.3** The refinement model is loaded at deployment start and after a variant change, as before.

### US11 — Platform
*As a Frinks support engineer, I want to know which shipped pieces change, so that the release is packaged right.*
- **Kind:** Platform
- **Parts:** engine-runtime-packaging, edge-release-upgrade, edge-health
- **Teams:** SDE + AI
- **Stakeholders:** Swayam (SDE), Shreyas (AI)
- **Covers:** C13, C17, C18

- **AC11.1** Node Docker image: no change.
- **AC11.2** Python Docker image: no change.
- **AC11.3** Scripts bundle: changes; it carries the updated Vision, Edge and Python code.
- **AC11.4** Installer: no change.
- **AC11.5** Error codes: no new codes.
- **AC11.6** Release note: "The Matting module is now called Edge Refinement (module prefix ER) in Frinks Vision and Frinks Edge."

### US12 — Migration requirements
*As a Frinks support engineer, I want existing data and mixed versions handled, so that the rename breaks nothing already saved.*
- **Kind:** Migration
- **Parts:** vision-recipe-tree, vision-auto-annotation, vision-instance-creation, edge-instance-details, edge-backup-migration, edge-release-upgrade
- **Teams:** SDE + AI
- **Stakeholders:** Swayam (SDE), Shreyas (AI)
- **Covers:** C6, C7, C17

- **AC12.1** Existing data in Vision: the release's database script renames the module-type entry to edgeRefinement / Edge Refinement and points existing modules of this type at it, so the add-module list shows only "Edge Refinement"; the module's settings table keeps its name.
- **AC12.2** A Vision recipe that already contains the module shows it as Edge Refinement after the release, with its classes and outputs unchanged.
- **AC12.3** Saved auto-annotation settings keep their toggle on or off as it was before the release.
- **AC12.4** Older Edge with a new instance: a package that contains the module is not supported on an Edge older than this release (no site uses the module); packages without it upload as before.
- **AC12.5** Older instance on the new Edge: an instance without the module uploads, deploys and runs unchanged.
- **AC12.6** (removed): covered by AC6.2 in the earlier version; no longer applies.
- **AC12.7** Upgrades of Edge boxes in the field rename the module-type entry in the Edge database and change nothing else.
- **AC12.8** (removed): the settings table is not renamed, so the Edge table layout does not change.
- **AC12.9** The release is tested end to end on staging, with Vision, Edge and every Python job together, before it ships.

## 7. Out of scope
**Phase 2 (later, own PRD):**
- Renaming the module's settings table and every internal name in the servers and Python code (files, classes, the type value Python uses).
- Setting names on machines (e.g. the model-path settings) and model, debug and folder names.
- The chat assistant manuals.
- The unused model step in the Python Docker image recipe.

**Not planned:**
- The copied open-source ViTMatte model code keeps its own names (PM, 27 Oct). Two of them are stored inside the model file and must never change.
- Two old client-specific repos that contain the word: not shipped (PM, 27 Oct).
- The old Jira epics named after the module and the "Matting" wording in the Module Outputs in Reports & Analytics PRD: handled separately.

## 8. How we'll know it worked
| Measure | Target |
|---|---|
| Staging run: recipe with ER_1 trained, recommended, auto-annotated, deployed and run on Edge, with the inspect tools open on every screen used | No screen, Network request or response, Console line or Sources file shows "matting" |
| Edge logs, Health and Vision cloud job logs from that run | No line contains "matting" |
| Same staging image before and after the release | Same refined masks and the same OK/NG |

## Appendix A — Coverage
**Not applicable:**
- **C2** — N/A: the name applies wherever the module is used; there is no project, capture or instance-level choice.
- **C3** — N/A: creating, copying and deleting the module work as before.
- **C5** — N/A: no new states; the changed error texts are AC1.5 and AC1.6.
- **C11** — N/A: the module is not in Edit Module Configurations on Edge.
- **C15** — N/A: no network, offline or time behaviour changes.
- **C16** — N/A: no licence, encryption or data-leaving change; the model file is unchanged.

**Coverage check:** PASS (check_prd.py, 28 Oct 2026).

## Appendix B — Code references
For developers. The full place-by-place list is in matting-exposure-audit.xlsx; phase 1 needs only the rows seen on screens, in the browser and in logs.
- **Module-type entry (browser sees it):** `frinksvision-backend`: `src/config/populate.ts:215-216` (inserted only if missing: 368-378), served by `GET /store/module` (`frinksvision-frontend` `moduleWorkflow/index.tsx:456`); every Vision and Edge comparison against the entry's id ('matting') must follow the rename: `frinksvision-backend` `src/dao/module.ts:480, 713, 824, 1759`, `src/service/module.ts:54-96`, `src/service/pythonModel.ts:686`, `src/utils/module.ts:8, 24`; `frinksedge-deployment-backend` `src/config/populate.ts:136-137`, `src/dao/module.ts:465, 569`, `src/cache/keystore.ts:595-596`, `src/service/pythonModel.ts:200, 298, 360, 934`, `src/service/instance.ts:135, 2636`, `src/moduleTree/nodes/moduleNode.ts:8`; `frinksedge-inferencing-backend` `src/python-model/transformers/matting.transformer.ts:4`, `src/python-model/python-model-result.service.ts:21, 141, 186`, `src/shared/infrastructure/module/module.repository.ts:336, 442`. Settings table `matting_config` keeps its name.
- **Vision screens and frontend code:** `frinksvision-frontend`: `moduleWorkflow/selectedModule.tsx:60, 73, 1069, 1172, 2469-2473, 2726, 3143`; `moduleWorkflow/index.tsx:69-73`; `matting-module/` (folder and component names; inputs.tsx:121); `src/core/constants/index.ts:175` (MAT); `src/shared/copy/tooltips.ts:164`; `project-configuration/recipeFilters.ts:32`; `configure-auto-annotation/SegmentationRoi.tsx:119, 320-325`, `moduleItem.tsx:101`; `src/shared/hooks/annotationPipeline/annotationPipeline.tsx:508`.
- **Auto-annotation setting name in the browser:** `frinksvision-backend`: `src/service/annotation.ts:382`, `src/dto/Frontend.ts:1576, 1649`; read by `Auto-annotation` (live branches) `scripts/annotation/modules/segmentModule.py:137-151, 197-214`.
- **Edge keys:** `frinksedge-deployment-backend`: `src/cache/keystore.ts:595-596` (MAT), used by `keystore.ts:536`, `src/keys/createKeys.ts:164`, `src/triggers/params.ts:215`.
- **Reports raw data:** the engine writes the type `vip-inference-python` `scripts/assembly/resultProcess/matting.py:431`; the result reaches Reports through `frinksedge-inferencing-backend` `src/python-model/python-model-result.service.ts`.
- **Edge server messages:** `frinksedge-inferencing-backend`: `src/shared/dto/dtos/parser.dto.ts:443-465`; `src/shared/infrastructure/module-config/matting/matting-config.repository.ts:6` (feature name printed in database errors).
- **Edge AI engine logs and Health:** `vip-inference-python`: `scripts/main.py:286`, `components/main_utils.py:37-54`, `modules/mattingModule.py:27-42`, `interfaces/mattingInterface.py:148, 243`, `resultProcess/matting.py:232-418`, `models/ModelManager.py:983-988` (MODELLOAD_005).
- **Vision cloud job logs:** `workflow-dataprocessor` `modules/mattingModule.py:30-69`, `interfaces/mattingInterface.py:18, 201`, `workflow/workflow_processor.py:964`; `train-utils` `io/downloader.py:188`; `Segmentation-Training` and `Segmentation-Evaluation` (`master` and live `create_build_dev`) `scripts/interface/mattingInterface.py:209`, `scripts/modules/mattingModule.py:19, 59`; `OCR-Training-Detection-ALL` `Snake/scripts/trainUtils/downloader.py:296`; `Auto-annotation` (live branches) `segmentModule.py:214`. The branch scan also found the word on live branches of Assembly-Training-OCR, assembly-training, classification-training-docker, Assembly-model-evaluation, Classification-Evaluation, Craft-model-evaluation and OCR-Evaluation; their log lines are in scope for AC5.3.
- **Database scripts:** Vision changes run by hand from `frinksvision-backend/release-sql/<feature>/`; Edge schema in `frinksedge-deployment-backend/dump.sql`.

Repos checked (27 Oct 2026): frinksvision-frontend @ df358e8, frinksvision-backend @ 1703f24, frinksedge-deployment-backend @ 5a69c77, frinksedge-deployment-frontend @ 857dfddd, frinksedge-inferencing-backend @ 310c18e, frinksedge-report-frontend @ 9893a19, frinksedge-health @ 2dbc622, vip-inference-python @ 3af02b5, Python-inference-docker @ 62e22bd (50_series), workflow-dataprocessor @ d2cd9bd, train-utils @ d3e6378, OCR-Training-Detection-ALL @ fc59f48, assembly-dataset-recommender @ 0556f06, Segmentation-Data-Recommender @ 58d1e07, Recommendation-classification @ 0796dbb, Recommendation-OCR @ ae1e953, Segmentation-Training @ f749aab (+ create_build_dev), Segmentation-Evaluation @ 44bb8c6 (+ create_build_dev), Auto-annotation @ d213b71 (+ auto-segment 6e56c5f), frinkschat-backend @ 88e5c91, and the other 14 synced repos (no mention on their default branch).

## Changelog
Every line names the US / AC ids it touched.
- 27 Oct 2026 — First draft, US1–US12 (AC1.1–AC12.9). Decisions: name Edge Refinement, prefix ER, internal name edgeRefinement; ViTMatte code left as-is; one release with no old-name support; database table renamed too; stakeholders Swayam (SDE) and Shreyas (AI); prototype not needed (Tanay Deo)
- 27 Oct 2026 — Duplicate pass: AC12.6 (removed): covered by AC6.2, which now names the schema-mismatch refusal; AC12.9 trimmed to the staging test (the one-release switch is AC6.2); AC11.5 trimmed (MODELLOAD_005 detail is AC5.4); AC11.1 trimmed (scripts bundle is AC11.3). Contradictions settled by earlier decisions: AC6.4 allows the image's unused model step until its next rebuild (default e) and covers shipped repos only (client repos not shipped) (Tanay Deo)
- 27 Oct 2026 — Approved. Owner set to Swayam (SDE) and Shreyas (AI); no AC changes (Tanay Deo)
- 28 Oct 2026 — Split into two phases; this PRD is phase 1 and back to In review. Phase 1 = screens, the browser's inspect tools (Network, Console, Sources) and error logs (Edge and Vision cloud jobs); the module-type entry is renamed in both databases, the settings table is not. US6 rewritten as "inspect tools never show the old name": AC6.1–AC6.4 replaced. US7 (removed): chat manuals to phase 2. AC5.5, AC5.6 (removed): setting and file names to phase 2. US4 and US6 Teams SDE. AC11.2 "no change" without the cleanup note. AC12.1, AC12.4, AC12.7 rewritten for the entry-only rename; AC12.8 (removed): no table layout change. Goals G1–G4 replace G1–G3 (Tanay Deo)

## Checked, not affected
- vision-projects, vision-variants: the module has no project or variant setting.
- mod-segmentation: the module's only parent; nothing in Segmentation itself changes (its auto-annotation toggle is under vision-auto-annotation).
- mod-bead-inspection, mod-classification, mod-code-reader, mod-color-detection, mod-input-master-image, mod-ocr, mod-odc, mod-search-area, mod-tracker: never linked to this module.
- edge-emc: the module is not in Edit Module Configurations.
- edge-live-dashboard: shows widgets bound to keys; key names are covered under Dashboard Builder (US4), and no site has a dashboard bound to this module.
- edge-storage-image-saving: no saved image or folder is named after the module on Edge.
- edge-access-audit, edge-camera-capture, edge-encryption-keys, edge-installer, edge-networking, edge-plc-setup, edge-system-time: no link to the module or its name.
- Code back-check: mod-bead-inspection, mod-classification, mod-code-reader, mod-input-master-image, mod-ocr, mod-odc, mod-search-area, mod-segmentation, mod-tracker, vision-projects, vision-variants, edge-access-audit, edge-camera-capture, edge-emc, edge-encryption-keys and edge-storage-image-saving share files with this change (the module, instance and result services, the Edge database script, the engine's model loader), but only the lines for this module change there.
