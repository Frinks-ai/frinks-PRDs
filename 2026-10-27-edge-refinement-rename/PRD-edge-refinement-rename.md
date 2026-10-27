# PRD: Rename Matting to Edge Refinement

**Prototype:** None — no new screens or layouts; only names and wording change (PM decision, 27 Oct 2026)
**Product:** Vision + Edge
**Status:** Approved
**Date:** 27 October 2026
**Owner:** Swayam (SDE), Shreyas (AI)
**Related:** Original module build: FP-2454 (epic) with FP-2343, FP-2349, FP-2352, FP-2356 and stories FP-2344 to FP-2363. Same principle: FP-3473 (model names removed from logs and screens). Full place-by-place list: matting-exposure-audit.xlsx (shared separately).
**Code checked:** 27 October 2026 — 34 repos at their latest default-branch commit, plus the live branches of Auto-annotation, Segmentation-Training and Segmentation-Evaluation (list in Appendix B)

---

## Discussion points
None.

## 1. Summary
- The module that sharpens the edges of segmentation masks is called **Edge Refinement** everywhere: Vision screens, server messages, Edge keys, reports, logs, settings and file names.
- New modules of this type are named **ER_1, ER_2, …** in Vision, and their Edge keys use **ER**.
- Inside the product the module's internal name becomes **edgeRefinement**: in the data Vision sends to Edge and to the AI engine, in both databases, and in every Python job.
- The module works exactly as before: same refined masks, same OK/NG, same place in the recipe tree.
- Everything switches in one release. No part keeps understanding the old name.
- The only place the word stays is the copied open-source model code (ViTMatte), which nobody sees in the product.

## 2. Why we are making this
- **Today:** the module shows its research name, "Matting", on the add-module list, panel title, recipe filters, auto-annotation settings, error pop-ups and Edge key names (MAT). The same name appears in logs, error details, setting names and file names on Edge boxes and in training jobs. About 700 places in 20 repos use it.
- **Evidence:** product naming decision: the product never shows the academic name of a technique, the same principle as the model-name clean-up in FP-3473.
- **After:** every place a user, integrator or support engineer can see says "Edge Refinement", and the module behaves the same.

## 3. Goals and non-goals
**Goals**
- G1. No screen, message, key, report, log, setting or file name says "Matting"; they say "Edge Refinement" or ER.
- G2. One internal name, edgeRefinement, across Vision, Edge and every Python job, shipped together in one release.
- G3. The module and everything around it keeps working exactly as before.

**Non-goals**
- Changing what the module does, its settings or its place in the recipe tree.
- Renaming the copied open-source ViTMatte model code.
- Adding the module to Edit Module Configurations on Edge.

## 4. Key concepts
- **Edge Refinement:** the new name of the Matting module. It takes a Segmentation module's masks and sharpens their edges.
- **ER:** the new name prefix for these modules (ER_1) and in their Edge key names. It replaces MAT.
- **edgeRefinement:** the module's internal name, used in data, databases and code. It replaces matting.
- **ViTMatte:** the free, published research model the module runs. Its copied code keeps its own names, two of which are stored inside the model file.

## 5. Impact map
The parts this changes. Parts checked and not affected are listed at the very end.

| Part | What will happen | User stories |
|---|---|---|
| Matting (mod-matting) | Called Edge Refinement everywhere; works the same | US1, US6, US10 |
| Recipe editor (vision-recipe-tree) | Add-module list, panel title, tooltip, pop-ups, server messages and new module names say Edge Refinement / ER; parent rules unchanged; database entry and table renamed | US1, US6, US9, US12 |
| Captures list (vision-captures-cameras) | Recipe Management filter option and module summary say Edge Refinement | US2 |
| Auto-annotation (vision-auto-annotation) | Toggle label says Edge Refinement; job logs and settings renamed; toggle works as before; saved on/off values kept | US3, US5, US9, US12 |
| Datasets and annotations (vision-datasets-annotations) | A capture with the module still goes through the sample-data steps | US9 |
| Instance creation (vision-instance-creation) | The package carries the module under its new name; the new Edge version's table layout is registered | US6, US12 |
| Model training (vision-model-training) | Training jobs fetch the refinement model under new setting and file names; logs renamed | US5, US9 |
| Model evaluation (vision-model-evaluation) | Same as training, for evaluation jobs | US5, US9 |
| Data Recommender (vision-training-sets-recommender) | Same as training, for recommender runs | US5, US9 |
| Access (vision-access) | No role or permission change | US8 |
| Masking (mod-masking) | Still accepts Edge Refinement as its parent and gets refined masks | US9, US10 |
| Orientation & Tilt Check (mod-orientation) | Still accepts Edge Refinement as its parent and gets refined masks | US9, US10 |
| Shape Extractor (mod-shape-extractor) | Still accepts Edge Refinement as its parent and gets refined masks | US9, US10 |
| Result (mod-result) | Same pass/fail for the module | US10 |
| AI engine (engine-workflow) | Runs the module under its new name; logs and settings renamed | US5, US6, US10 |
| AI engine images (engine-runtime-packaging) | No image change | US11 |
| Instance upload (edge-instance-details) | Accepts packages with the new name; refuses old-name packages | US6, US12 |
| Deployment (edge-deployment) | Sends the module to the AI engine under its new name; messages renamed | US5, US6 |
| Inferencing (edge-inferencing-customization) | Reads the module's settings and results under the new name; messages renamed | US5, US10 |
| Actions (edge-actions) | Key names use ER | US4 |
| PLC Read (edge-plc-read) | Key names use ER | US4 |
| PLC Write (edge-plc-write) | Key names use ER | US4 |
| Internal Actions (edge-internal-actions) | Key names use ER | US4 |
| Dashboard Builder (edge-dashboard-builder) | Key names use ER | US4 |
| Reports (edge-reports-analytics) | Raw result data names the module edgeRefinement | US4 |
| Health (edge-health) | Model-load error detail says Edge Refinement; no new error codes | US5, US11 |
| Model loading (edge-model-loading-variant-change) | Loads the refinement model as before | US10 |
| Backup and migration (edge-backup-migration) | Edge upgrade renames the module's table and entry | US12 |
| Releases (edge-release-upgrade) | Scripts bundle changes; Vision, Edge and Python ship together | US11, US12 |
| Chat assistant manuals (not in the component map) | Answers say Edge Refinement | US7 |

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
- **Teams:** SDE + AI
- **Stakeholders:** Swayam (SDE), Shreyas (AI)
- **Covers:** C9, C12
- **Today:** this module's keys carry MAT as their module type, and the raw result data in Reports names the module "matting".

- **AC4.1** Keys created for this module carry ER as their module type wherever keys are listed: PLC Read Config, PLC Write Config, Internal Action Config and Dashboard Builder.
- **AC4.2** The raw result data of a cycle in Reports & Analytics names this module's type "edgeRefinement".

### US5 — Logs, errors, settings and files say Edge Refinement
*As a Frinks support engineer, I want every log line, error detail, setting and file about this module to use the new name, so that the old name never shows up on a plant machine or in a job log.*
- **Kind:** Feature
- **Parts:** engine-workflow, edge-inferencing-customization, edge-deployment, edge-health, vision-model-training, vision-model-evaluation, vision-training-sets-recommender, vision-auto-annotation
- **Teams:** SDE + AI
- **Stakeholders:** Swayam (SDE), Shreyas (AI)
- **Covers:** C13, C17
- **Today:** logs, validation and database error messages, the model-load error detail, setting names and file names say "matting".

- **AC5.1** Running a recipe with the module on Edge writes no log line containing "matting"; every line about it says "Edge Refinement".
- **AC5.2** Edge server messages about this module (result checks and database errors) say "Edge Refinement".
- **AC5.3** Training, evaluation, Data Recommender and auto-annotation jobs that use the module write no log line containing "matting".
- **AC5.4** When the refinement model fails to load, the error detail shown for MODELLOAD_005 in Health reads "Edge Refinement model loading".
- **AC5.5** The settings that point to the refinement model are named EDGE_REFINEMENT_MODEL_PATH, EDGE_REFINEMENT_MODEL_LINK and EDGE_REFINEMENT_WARMUP_SIZE in every job and on Edge.
- **AC5.6** Model and debug files written by these jobs are named after Edge Refinement (e.g. edge_refinement_model.pth, debug_edge_refinement), never "matting".

### US6 — One internal name everywhere
*As a Frinks support engineer, I want the module's internal name to be edgeRefinement end to end, so that raw data, databases and code never carry the old name.*
- **Kind:** Feature
- **Parts:** mod-matting, vision-recipe-tree, vision-instance-creation, edge-instance-details, edge-deployment, engine-workflow
- **Teams:** SDE + AI
- **Stakeholders:** Swayam (SDE), Shreyas (AI)
- **Covers:** C7
- **Today:** Vision, Edge and every Python job identify the module as "matting".

- **AC6.1** A recipe with the module, sent from Vision to training jobs and packaged for Edge, names it edgeRefinement, and Edge passes that name to the AI engine.
- **AC6.2** No part of the release accepts the old name: Vision, Edge and the Python jobs recognise only edgeRefinement, and the new Edge refuses a package that still names the module the old way with today's schema-mismatch message (no site has one).
- **AC6.3** On a new installation, the Vision and Edge databases store the module's entry and settings under the Edge Refinement name, with no table or entry named "matting".
- **AC6.4** A word search for "matting" across all shipped Frinks repos finds it only inside the copied ViTMatte model code and in the unused model step of the Python Docker image recipe, which goes at its next rebuild (AC11.2); generated files committed by mistake that contain it are deleted.

### US7 — The chat assistant says Edge Refinement
*As a project member asking the Frinks chat assistant, I want answers to use the new name, so that the assistant matches the product.*
- **Kind:** Feature
- **Parts:** Chat assistant manuals (not in the component map)
- **Teams:** AI
- **Covers:** C18
- **Today:** the manuals behind the chat assistant say "Matting" (for example, "Segmentation and Matting cannot both be applied to the same Search Area").

- **AC7.1** Asked about the module, the chat assistant answers with "Edge Refinement" and never "Matting"; the PM team updates the manual text and the assistant's owner reloads it.

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
- **AC11.2** Python Docker image: no change. Edge reads the refinement model from its own models folder, not from the copy inside the image; that unused copy is cleaned up whenever the image is next rebuilt.
- **AC11.3** Scripts bundle: changes; it carries the renamed Vision, Edge and Python code.
- **AC11.4** Installer: no change; it sets no setting or folder with the old name.
- **AC11.5** Error codes: no new codes.
- **AC11.6** Release note: "The Matting module is now called Edge Refinement (module prefix ER) in Frinks Vision and Frinks Edge."

### US12 — Migration requirements
*As a Frinks support engineer, I want existing data and mixed versions handled, so that the rename breaks nothing already saved.*
- **Kind:** Migration
- **Parts:** vision-recipe-tree, vision-auto-annotation, vision-instance-creation, edge-instance-details, edge-backup-migration, edge-release-upgrade
- **Teams:** SDE + AI
- **Stakeholders:** Swayam (SDE), Shreyas (AI)
- **Covers:** C6, C7, C17

- **AC12.1** Existing data in Vision: the release's database script renames the module's entry and settings table, so the add-module list shows only "Edge Refinement" and no table or entry named "matting" remains.
- **AC12.2** A Vision recipe that already contains the module shows it as Edge Refinement after the release, with its classes and outputs unchanged.
- **AC12.3** Saved auto-annotation settings keep their toggle on or off as it was before the release.
- **AC12.4** Older Edge with a new instance: a package that contains the module, downloaded without a target version, is refused by an older Edge with today's schema-mismatch message.
- **AC12.5** Older instance on the new Edge: an instance without the module uploads, deploys and runs unchanged.
- **AC12.6** (removed): covered by AC6.2.
- **AC12.7** Upgrades of Edge boxes in the field rename the module's table and entry in the Edge database and change nothing else.
- **AC12.8** The new Edge version's table layout is registered in Vision, so an instance downloaded for it includes the module's settings.
- **AC12.9** The release is tested end to end on staging, with Vision, Edge and every Python job together, before it ships.

## 7. Out of scope
- The copied open-source ViTMatte model code keeps its own names (PM, 27 Oct). Two of them are stored inside the model file and must never change.
- The old Jira epics named after the module (FP-2454 and others) and the "Matting" wording in the Module Outputs in Reports & Analytics PRD: handled separately after this PRD is approved.
- Two old client-specific repos that contain the word: not shipped (PM, 27 Oct).
- Fix version: added when the tickets are made.

## 8. How we'll know it worked
| Measure | Target |
|---|---|
| Word search for "matting" across all shipped Frinks repos, default and live branches | Only the copied ViTMatte model code, plus the image's unused model step until its next rebuild |
| Staging run: recipe with ER_1 trained, recommended, deployed and run on Edge | No screen, key, report, log or setting shows "matting" |
| Same staging image before and after the release | Same refined masks and the same OK/NG |

## Appendix A — Coverage
**Not applicable:**
- **C2** — N/A: the name applies wherever the module is used; there is no project, capture or instance-level choice.
- **C3** — N/A: creating, copying and deleting the module work as before; copies carry the same internal name.
- **C5** — N/A: no new states; the changed error texts are AC1.5 and AC1.6.
- **C11** — N/A: the module is not in Edit Module Configurations on Edge.
- **C15** — N/A: no network, offline or time behaviour changes.
- **C16** — N/A: no licence, encryption or data-leaving change; the model file is unchanged.

**Coverage check:** (pasted below after the final run)

## Appendix B — Code references
For developers. The full place-by-place list (736 rows, with suggested change) is in matting-exposure-audit.xlsx.
- **Recipe editor:** `frinksvision-frontend`: `src/modules/project/module-configuration/components/moduleWorkflow/index.tsx:456` (add-module list from the server), `:69-73` (allowed parents); `moduleWorkflow/selectedModule.tsx:60, 2469-2473, 2726, 3143`; `matting-module/` (inputs.tsx:121); `src/core/constants/index.ts:175` (MAT prefix); `src/shared/copy/tooltips.ts:164`. `frinksvision-backend`: `src/config/populate.ts:215-216` (module entry, inserted only if missing: 368-378); `src/service/module.ts:54-96` (parent-rule messages); `src/db/mattingConfig.schema.ts:12, 21`; `src/dao/module.ts:480, 713, 824, 1759`.
- **Captures list:** `frinksvision-frontend`: `src/modules/project/project-configuration/recipeFilters.ts:32`. `frinksvision-backend`: `src/utils/module.ts:8` (summary label), `:20-25` (sample-data rule).
- **Auto-annotation:** `frinksvision-frontend`: `src/modules/project/configure-auto-annotation/SegmentationRoi.tsx:119, 320-325`, `moduleItem.tsx:101`. `frinksvision-backend`: `src/service/annotation.ts:382` (saved setting), `src/dto/Frontend.ts:1576, 1649`. `Auto-annotation` (live branches auto-segment, feature/*; not on `main`): `scripts/annotation/modules/segmentModule.py:35-37, 137-151, 197-214`, `scripts/components/getConfig.py:404-408`, `scripts/modules/moduleRegistry.py:7, 23`, `ANNOTATION.env:36-37`.
- **Instance creation and upload:** `frinksvision-backend`: `src/service/instance.ts:82, 926-929, 1151, 1540`; `src/service/pythonModel.ts:686-688`. `frinksedge-deployment-backend`: `src/service/uploadValidation.ts:1070`; `src/service/instance.ts:94, 135, 1418, 2636`; `src/dao/instances.ts:993, 1057, 1104`; `dump.sql:191, 1338-1344, 2566-2567`; `src/config/populate.ts:136-137`.
- **Edge keys:** `frinksedge-deployment-backend`: `src/cache/keystore.ts:595-596` (MAT), used by `keystore.ts:536`, `src/keys/createKeys.ts:164`, `src/triggers/params.ts:215`.
- **Deployment and inferencing:** `frinksedge-deployment-backend`: `src/service/pythonModel.ts:200, 298, 360, 934-936`; `src/moduleTree/manager.ts:7, 324-350`; `src/dao/mattingConfig.ts`. `frinksedge-inferencing-backend`: `src/python-model/transformers/matting.transformer.ts`; `src/python-model/python-model-result.service.ts:21, 141, 186`; `src/shared/dto/dtos/parser.dto.ts:443-465, 951-958`; `src/shared/infrastructure/module-config/matting/` (feature name printed in database errors: `matting-config.repository.ts:6`); `src/shared/infrastructure/module/module.repository.ts:336-442`; `src/module-tree/module-tree.service.ts:308-326`.
- **AI engine:** `vip-inference-python`: `scripts/assembly/modules/moduleRegistry.py:30`, `modules/mattingModule.py`, `interfaces/mattingInterface.py:142-148, 243`, `resultProcess/matting.py:232-432`, `models/model_keys.py:31`, `models/ModelManager.py:973-988` (MODELLOAD_005), `components/main_utils.py:21-61` (model path setting), `interfaces/orientationInterface.py:174`, `modules/validation/module_validators.py:149`, `scripts/main.py:284-286`. Weights are loaded by layer name (`models/matting/matting.py:53`); `vitmatte/decoder/detail_capture.py:94, 136` (`matting_head`, `matting_convs`) must not be renamed.
- **AI engine images:** `Python-inference-docker`: `Dockerfile:139-145` (unused copy). Edge mounts its models folder: `frinksedge-deployment-backend`: `docker-files/docker-compose.yaml:33`.
- **Training, evaluation, recommender:** `train-utils`: `src/train_utils/io/downloader.py:175-191` (also committed `__pycache__` files). `workflow-dataprocessor`: `src/workflow_dataprocessor/modules/mattingModule.py`, `interfaces/mattingInterface.py:16-19, 125, 201`, `workflow/workflow_processor.py:947-1158`. `Segmentation-Training` (`master` and live `create_build_dev`): `{unet,pointrend}-segmentation/scripts/downloader.py:201-209`, `scripts/interface/mattingInterface.py:14-15, 122, 209`, `scripts/modules/mattingModule.py:8-59`. `Segmentation-Evaluation` (`master` and live `create_build_dev`): same layout. `OCR-Training-Detection-ALL`: `{CRAFT,psenet,Snake}/scripts/trainUtils/downloader.py:273-296`, `TRAINING.env` files (also a committed `Snake/scripts/.coverage`). Recommenders: `scripts/components/getConfig.py` in `assembly-dataset-recommender` (348-352), `Segmentation-Data-Recommender` (325-329), `Recommendation-classification` (226-230), `Recommendation-OCR` (232-236), and their `.env` files.
- **Chat assistant manuals:** `frinkschat-backend`: `Frinks-Manuals-Query-Engine/Frinks Vision/manuals-text/Segmentation_Setup.md:58, 117, 144-145`, `Image_Annotation.md:78`, `reference/DICTIONARY.md:128`, `reference/ABBREVIATIONS.md:18`, `CLAUDE.md:38`.
- **Database scripts:** Vision changes run by hand from `frinksvision-backend/release-sql/<feature>/` (the backend never runs them); Edge schema lives in `frinksedge-deployment-backend/dump.sql`.

Repos checked (27 Oct 2026): frinksvision-frontend @ df358e8, frinksvision-backend @ 1703f24, frinksedge-deployment-backend @ 5a69c77, frinksedge-deployment-frontend @ 857dfddd, frinksedge-inferencing-backend @ 310c18e, frinksedge-report-frontend @ 9893a19, frinksedge-health @ 2dbc622, vip-inference-python @ 3af02b5, Python-inference-docker @ 62e22bd (50_series), workflow-dataprocessor @ d2cd9bd, train-utils @ d3e6378, OCR-Training-Detection-ALL @ fc59f48, assembly-dataset-recommender @ 0556f06, Segmentation-Data-Recommender @ 58d1e07, Recommendation-classification @ 0796dbb, Recommendation-OCR @ ae1e953, Segmentation-Training @ f749aab (+ create_build_dev), Segmentation-Evaluation @ 44bb8c6 (+ create_build_dev), Auto-annotation @ d213b71 (+ auto-segment 6e56c5f), frinkschat-backend @ 88e5c91, and the other 14 synced repos (no mention).

## Changelog
Every line names the US / AC ids it touched.
- 27 Oct 2026 — First draft, US1–US12 (AC1.1–AC12.9). Decisions: name Edge Refinement, prefix ER, internal name edgeRefinement; ViTMatte code left as-is; one release with no old-name support; database table renamed too; stakeholders Swayam (SDE) and Shreyas (AI); prototype not needed (Tanay Deo)
- 27 Oct 2026 — Duplicate pass: AC12.6 (removed): covered by AC6.2, which now names the schema-mismatch refusal; AC12.9 trimmed to the staging test (the one-release switch is AC6.2); AC11.5 trimmed (MODELLOAD_005 detail is AC5.4); AC11.1 trimmed (scripts bundle is AC11.3). Contradictions settled by earlier decisions: AC6.4 allows the image's unused model step until its next rebuild (default e) and covers shipped repos only (client repos not shipped) (Tanay Deo)
- 27 Oct 2026 — Approved. Owner set to Swayam (SDE) and Shreyas (AI); no AC changes (Tanay Deo)

## Checked, not affected
- vision-projects, vision-variants: the module has no project or variant setting.
- mod-segmentation: the module's only parent; nothing in Segmentation itself changes (its auto-annotation toggle is under vision-auto-annotation).
- mod-bead-inspection, mod-classification, mod-code-reader, mod-color-detection, mod-input-master-image, mod-ocr, mod-odc, mod-search-area, mod-tracker: never linked to this module.
- edge-emc: the module is not in Edit Module Configurations.
- edge-live-dashboard: shows widgets bound to keys; key names are covered under Dashboard Builder (US4), and no site has a dashboard bound to this module.
- edge-storage-image-saving: no saved image or folder is named after the module on Edge.
- edge-access-audit, edge-camera-capture, edge-encryption-keys, edge-installer, edge-networking, edge-plc-setup, edge-system-time: no link to the module or its name.
- Code back-check: mod-bead-inspection, mod-classification, mod-code-reader, mod-input-master-image, mod-ocr, mod-odc, mod-search-area, mod-segmentation, mod-tracker, vision-projects, vision-variants, edge-access-audit, edge-camera-capture, edge-emc, edge-encryption-keys and edge-storage-image-saving share files with this change (the module, instance and result services, the Edge database script, the engine's model loader), but only the lines for this module change there.
