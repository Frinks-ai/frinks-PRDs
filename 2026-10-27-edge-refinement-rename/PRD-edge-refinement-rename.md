# PRD: Rename Matting to Edge Refinement

**Prototype:** None — no new screens or layouts; only names and wording change (PM decision, 27 Oct 2026)
**Product:** Vision + Edge
**Status:** Approved
**Date:** 29 October 2026
**Owner:** Swayam (SDE POC)
**Timeline:** SPRINT 26-15 (running sprint; tickets created there on 28 Oct 2026)
**Related:** Epic FP-3895 with story FP-3896 (assigned to Deb). Original module build: FP-2454 with FP-2343, FP-2349, FP-2352, FP-2356 and stories FP-2344 to FP-2363. Same principle: FP-3473 (model names removed from logs and screens). Full place-by-place list: matting-exposure-audit.xlsx (shared separately).
**Code checked:** 27 October 2026 — 34 repos at their latest default-branch commit, plus the live branches of Auto-annotation, Segmentation-Training and Segmentation-Evaluation (list in Appendix B)

---

## Discussion points
None.

## 1. Summary
- Everything a user can see says **Edge Refinement**, never "Matting": Vision and Edge screens and pop-ups, error details (including the Health page), the browser's inspect tools (Network, Console, Sources), files users download, and the chat assistant's answers.
- New modules of this type are named **ER_1, ER_2, …** in Vision. No Matting modules exist in the Vision production database, so no existing module is renamed.
- The module type id becomes **edgeRefinement** in the Vision and Edge databases and in everything the browser receives; the module's settings table is renamed too.
- What no user can see keeps the old name: code file and class names, the AI engine's internal type, settings and log lines, and the data Vision and Edge send to the AI engine and the cloud jobs.
- The module works exactly as before: same refined masks, same OK/NG, same place in the recipe tree. Vision and Edge ship together in one release.

## 2. Why we are making this
- **Today:** the module shows its research name, "Matting", on the add-module list, panel title, recipe filters, auto-annotation settings, error pop-ups and Edge key names (MAT). The same name appears in logs, error details, setting names and file names on Edge boxes and in training jobs. About 700 places in 20 repos use it.
- **Evidence:** product naming decision: the product never shows the academic name of a technique, the same principle as the model-name clean-up in FP-3473.
- **After:** every place a user, integrator or support engineer can see says "Edge Refinement", and the module behaves the same. Places only code can reach keep the old name.

## 3. Goals and non-goals
**Goals**
- G1. No screen, pop-up, error detail (including the Health page), key, report, downloaded file or chat answer, and nothing in the browser's inspect tools (Network, Console, Sources), says "Matting"; they say "Edge Refinement" or ER.
- G2. The module type id and the settings table carry the new name in the Vision and Edge databases, shipped together in one release.
- G3. The module and everything around it keeps working exactly as before.

**Non-goals**
- Changing what the module does, its settings or its place in the recipe tree.
- Renaming the copied open-source ViTMatte model code.
- Adding the module to Edit Module Configurations on Edge.
- Renaming anything no user can see: code files, folders and classes, the AI engine's internal type, settings and log lines (they appear only in the downloadable log zip), and the data passed between servers and containers.

## 4. Key concepts
- **Edge Refinement:** the new name of the Matting module. It takes a Segmentation module's masks and sharpens their edges.
- **ER:** the new name prefix for these modules (ER_1) and in their Edge key names. It replaces MAT.
- **edgeRefinement:** the module type id in the Vision and Edge databases and in everything the browser receives. It replaces matting there; the AI engine and the cloud jobs keep matting internally.
- **ViTMatte:** the free, published research model the module runs. Its copied code keeps its own names, two of which are stored inside the model file.

## 5. Impact map
The parts this changes. Parts checked and not affected are listed at the very end.

| Part | What will happen | User stories |
|---|---|---|
| Matting (mod-matting) | Shown as Edge Refinement wherever a user looks; works the same | US1, US6, US10 |
| Recipe editor (vision-recipe-tree) | Add-module list, panel title, tooltip, pop-ups, server messages and module names say Edge Refinement / ER; parent rules unchanged; type entry and table renamed | US1, US6, US9, US12 |
| Captures list (vision-captures-cameras) | Recipe Management filter option, saved filters and module summary say Edge Refinement | US2 |
| Auto-annotation (vision-auto-annotation) | Toggle label says Edge Refinement; the browser never sees the old setting name; toggle works as before; saved on/off values kept | US3, US6, US9, US12 |
| Datasets and annotations (vision-datasets-annotations) | A capture with the module still goes through the sample-data steps | US9 |
| Instance creation (vision-instance-creation) | The package carries the renamed type and table; downloads with the module for an older Edge are refused | US6, US12 |
| Model training (vision-model-training) | Jobs are not changed and keep receiving the old internal name; any of their messages a Vision screen shows has no "matting" | US5, US9 |
| Model evaluation (vision-model-evaluation) | Same as training, for evaluation jobs | US5, US9 |
| Data Recommender (vision-training-sets-recommender) | Same as training, for recommender runs | US5, US9 |
| Access (vision-access) | No role or permission change | US8 |
| Masking (mod-masking) | Still accepts Edge Refinement as its parent and gets refined masks | US9, US10 |
| Orientation & Tilt Check (mod-orientation) | Still accepts Edge Refinement as its parent and gets refined masks | US9, US10 |
| Shape Extractor (mod-shape-extractor) | Still accepts Edge Refinement as its parent and gets refined masks | US9, US10 |
| Result (mod-result) | Same pass/fail for the module | US10 |
| AI engine (engine-workflow) | Keeps its internal name; only the model-load error detail changes | US5, US10 |
| AI engine images (engine-runtime-packaging) | No image change | US11 |
| Instance upload (edge-instance-details) | Accepts packages with the new name; refuses old-name packages | US6, US12 |
| Deployment (edge-deployment) | Keeps sending the old internal name to the AI engine; screens and their data show the new name; messages renamed | US5, US6 |
| Inferencing (edge-inferencing-customization) | Renames the module type when it reads the AI engine's results; messages renamed | US4, US5, US10 |
| Actions (edge-actions) | No key contains MATTING; keys for the module, if created, use ER | US4 |
| PLC Read (edge-plc-read) | No key contains MATTING | US4 |
| PLC Write (edge-plc-write) | No key contains MATTING | US4 |
| Internal Actions (edge-internal-actions) | No key contains MATTING | US4 |
| Dashboard Builder (edge-dashboard-builder) | No key contains MATTING | US4 |
| Reports (edge-reports-analytics) | Raw result data names the module edgeRefinement | US4 |
| Health (edge-health) | Model-load error detail and database error tag say Edge Refinement; no new error codes | US5, US11 |
| Model loading (edge-model-loading-variant-change) | Loads the refinement model as before | US10 |
| Backup and migration (edge-backup-migration) | Edge upgrade renames the module's table and type entry and points existing modules at it | US12 |
| Releases (edge-release-upgrade) | Scripts bundle changes; Vision and Edge ship together; field boxes checked for the database version | US11, US12 |
| Chat assistant manuals (not in the component map) | Answers say Edge Refinement | US13 |

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
- **AC2.3** A Recipe Management filter saved before the release shows "Edge Refinement" and still lists the recipes that have the module.

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
- **Parts:** edge-actions, edge-plc-read, edge-plc-write, edge-internal-actions, edge-dashboard-builder, edge-reports-analytics, edge-inferencing-customization
- **Teams:** SDE
- **Covers:** C9, C12
- **Today:** the module has no keys today, but the key mapping would name it MATTING if it had any; the raw result data in Reports names the module "matting".

- **AC4.1** Keys for this module, if created, carry ER as their module type (proved by an automated test, since the module has no keys today).
- **AC4.2** The raw result data of a cycle in Reports & Analytics names this module's type "edgeRefinement".

### US5 — Error messages users can see say Edge Refinement
*As a Frinks support engineer, I want every error message and detail a user can see to use the new name, so that the old name never shows up on a screen.*
- **Kind:** Feature
- **Parts:** engine-workflow, edge-inferencing-customization, edge-deployment, edge-health, vision-model-training, vision-model-evaluation, vision-training-sets-recommender, vision-auto-annotation
- **Teams:** SDE + AI
- **Stakeholders:** Swayam (SDE POC, also does the AI work)
- **Covers:** C13, C17
- **Today:** validation and database error messages, the database error tag on the Health page and the model-load error detail say "matting".

- **AC5.1** (removed): the AI engine's log lines are only in the downloadable log zip, which no user sees (PM, 28 Oct 2026).
- **AC5.2** Edge server messages about this module that reach a screen, including database errors shown on the Health page, say "Edge Refinement".
- **AC5.3** (removed): covered by AC6.5, which checks every job message that reaches a Vision screen.
- **AC5.4** When the refinement model fails to load, the error detail shown for MODELLOAD_005 in Health reads "Edge Refinement model loading".
- **AC5.5** (removed): the AI engine's settings are not visible to users (PM, 28 Oct 2026).
- **AC5.6** (removed): file names in the Vision cloud jobs are out of scope; Edge writes no file named after the module.

### US6 — The browser and the databases use the new name
*As a Frinks support engineer, I want everything the browser receives and both databases to use edgeRefinement, so that the old name never reaches a user.*
- **Kind:** Feature
- **Parts:** mod-matting, vision-recipe-tree, vision-auto-annotation, vision-instance-creation, edge-instance-details, edge-deployment
- **Teams:** SDE
- **Covers:** C7
- **Today:** Vision and Edge send the module type "matting" and "Matting" to the browser and store it under that name.

- **AC6.1** A recipe with the module, packaged for Edge, names its type edgeRefinement. The AI engine and the training, evaluation, Data Recommender and auto-annotation jobs keep the old internal name: Edge and Vision keep sending it to them.
- **AC6.2** Vision and Edge accept only edgeRefinement from the browser and in instance packages, and the new Edge refuses a package that still names the module the old way with today's schema-mismatch message (no site has one).
- **AC6.3** On a new installation, the Vision and Edge databases store the module's entry and settings under the Edge Refinement name, with no table or entry named "matting".
- **AC6.4** (removed): a word search of the code is dropped; AC6.5 to AC6.8 check what users see (PM, 28 Oct 2026).
- **AC6.5** On every Vision screen, no data request or response in the browser inspect tool's Network tab (the Fetch/XHR and WebSocket entries) contains the word "matting", including job messages and chat answers, except text a user typed themselves (for example a module they named "Matting") and chat answers given before the release.
- **AC6.6** On every Edge screen (Deployments, Instance Details, Integrations, Analysed Image Configurations, Dashboard Builder, Reports & Analytics, Health), no data request or response in the Network tab (the Fetch/XHR and WebSocket entries) contains the word "matting", except text a user typed themselves and cycle results or Health errors recorded before the release.
- **AC6.7** Using Vision and Edge screens with the module writes nothing containing the word "matting" to the inspect tool's Console, except text a user typed themselves and data recorded before the release.
- **AC6.8** The Vision and Edge frontend code the browser loads (inspect tool's Sources tab) contains no word "matting".

### US7 — (removed): chat assistant manuals are out of scope (PM, 28 Oct 2026).

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
- **Stakeholders:** Swayam (SDE POC, also does the AI work)
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
- **Stakeholders:** Swayam (SDE POC, also does the AI work)
- **Covers:** C13, C17, C18

- **AC11.1** Node Docker image: no change.
- **AC11.2** Python Docker image: no change; the AI engine keeps its model folder and settings as they are.
- **AC11.3** Scripts bundle: changes; it carries the updated Vision and Edge code and the one changed AI engine message.
- **AC11.4** Installer: no change; it sets no setting or folder with the old name.
- **AC11.5** Error codes: no new codes.
- **AC11.6** Release note: "The Matting module is now called Edge Refinement (module prefix ER) in Frinks Vision and Frinks Edge."
- **AC11.7** Every Edge box in the field runs MariaDB 10.5.2 or newer, which the table rename needs; this is checked once before the release.

### US12 — Migration requirements
*As a Frinks support engineer, I want existing data and mixed versions handled, so that the rename breaks nothing already saved.*
- **Kind:** Migration
- **Parts:** vision-recipe-tree, vision-auto-annotation, vision-instance-creation, edge-instance-details, edge-backup-migration, edge-release-upgrade
- **Teams:** SDE + AI
- **Stakeholders:** Swayam (SDE POC, also does the AI work)
- **Covers:** C6, C7, C17

- **AC12.1** Existing data in Vision: the release's database script renames the module's entry and settings table, so the add-module list shows only "Edge Refinement" and no table or entry named "matting" remains.
- **AC12.2** (removed): no Matting modules exist in the Vision production database (PM, 29 Oct 2026).
- **AC12.3** Saved auto-annotation settings keep their toggle on or off as it was before the release.
- **AC12.4** Older Edge with a new instance: a package that contains the module, downloaded without a target version, is refused by an older Edge with today's schema-mismatch message.
- **AC12.5** Older instance on the new Edge: an instance without the module uploads, deploys and runs unchanged.
- **AC12.6** (removed): covered by AC6.2.
- **AC12.7** Upgrades of Edge boxes in the field rename the module's settings table and type entry in the Edge database, point existing modules of this type at the new entry, and change nothing else.
- **AC12.8** The new Edge version's table layout is registered in Vision, so an instance downloaded for it includes the module's settings.
- **AC12.9** The release is tested end to end on staging, with Vision, Edge and every Python job together, before it ships.
- **AC12.10** (removed): no Matting modules exist in the Vision production database, so there are no MAT_ modules to rename (PM, 29 Oct 2026).
- **AC12.11** Downloading an instance that contains the module for an Edge version older than this release is refused in Vision with a clear message naming the Edge version it needs.
- **AC12.12** An Edge box that already has the module deployed runs it as before after the upgrade and one re-deploy.

### US13 — The chat assistant says Edge Refinement
*As a project member asking the Frinks chat assistant, I want answers to use the new name, so that the assistant matches the product.*
- **Kind:** Feature
- **Parts:** Chat assistant manuals (not in the component map)
- **Teams:** SDE
- **Covers:** C18
- **Today:** the manuals behind the chat assistant say "Matting", and the assistant runs inside Vision, so its answers show on screen and in the Network tab.

- **AC13.1** Asked about the module, the chat assistant answers with "Edge Refinement".

## 7. Out of scope
- Code changes in the Vision cloud job repos (PM, 28 Oct 2026): Auto-annotation, Segmentation-Training, workflow-dataprocessor, train-utils, Segmentation-Evaluation, OCR-Training-Detection-ALL, the four Data Recommender repos and the seven training and evaluation repos on create_build_dev. They keep the old internal name, settings and file names; Vision keeps talking to them with it.
- The staging-environment notes (PM, 28 Oct 2026).
- Renaming what no user can see (PM, 28 Oct 2026): code files, folders and classes (the frontends ship without source maps, so these names never reach the browser; any word the browser does load is covered by AC6.8); the AI engine's internal type, settings, files and log lines; the data Edge sends to the AI engine.
- The copied open-source ViTMatte model code keeps its own names (PM, 27 Oct). Two of them are stored inside the model file and must never change.
- The old Jira epics named after the module (FP-2454 and others) and the "Matting" wording in the Module Outputs in Reports & Analytics PRD: handled separately after this PRD is approved.
- Two old client-specific repos that contain the word: not shipped (PM, 27 Oct).
- Fix version: added in Jira.

## 8. How we'll know it worked
| Measure | Target |
|---|---|
| Staging run with the inspect tools open on every Vision and Edge screen used | No Network, Console or Sources text contains the word "matting" (except user-typed text and data from before the release) |
| Staging run: recipe with ER_1 trained, recommended, deployed and run on Edge, with a forced database and model-load error | No screen, key, report, Health error or chat answer shows "matting" |
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
- **Recipe editor:** `frinksvision-frontend`: `src/modules/project/module-configuration/components/moduleWorkflow/index.tsx:456` (add-module list from the server), `:69-73` (allowed parents); `moduleWorkflow/selectedModule.tsx:60, 2469-2473, 2726, 3143`; `matting-module/` (inputs.tsx:121); `src/core/constants/index.ts:175` (MAT prefix); `src/shared/copy/tooltips.ts:164`. `frinksvision-backend`: `src/config/populate.ts:215-216` (module entry, inserted only if missing: 368-378); `src/service/module.ts:54-96` (parent-rule messages; types are lower-cased at `:19`, so the rule key is `edgerefinement`); `src/db/mattingConfig.schema.ts:12, 21`; `src/dao/module.ts:480, 713, 824, 1759`.
- **Captures list:** `frinksvision-frontend`: `src/modules/project/project-configuration/recipeFilters.ts:32` (option), `:537, 564` (filters saved in the browser's local storage). `frinksvision-backend`: `src/utils/module.ts:8` (summary label), `:20-25` (sample-data rule).
- **Auto-annotation:** `frinksvision-frontend`: `src/modules/project/configure-auto-annotation/SegmentationRoi.tsx:119, 320-325`, `moduleItem.tsx:101`. `frinksvision-backend`: `src/service/annotation.ts:382` (saved setting), `src/dto/Frontend.ts:1576, 1649`. `Auto-annotation` (live branches auto-segment, feature/*; not on `main`): `scripts/annotation/modules/segmentModule.py:35-37, 137-151, 197-214`, `scripts/components/getConfig.py:404-408`, `scripts/modules/moduleRegistry.py:7, 23`, `ANNOTATION.env:36-37`.
- **Instance creation and upload:** `frinksvision-backend`: `src/service/instance.ts:82, 926-929, 1151, 1540`; `src/service/pythonModel.ts:686-688`. `frinksedge-deployment-backend`: `src/service/uploadValidation.ts:1070`; `src/service/instance.ts:94, 135, 1418, 2636`; `src/dao/instances.ts:993, 1057, 1104`; `dump.sql:191, 1338-1344, 2566-2567`; `src/config/populate.ts:136-137`.
- **Edge keys:** `frinksedge-deployment-backend`: live mapping `src/keys/moduleStoreMap.ts:1-14` (no case for the module; the default upper-cases it to MATTING), used by `src/keys/createKeys.ts:164` and `src/triggers/params.ts:215`. `src/cache/keystore.ts:595-596` is the old version (its key service is commented out at `src/index.ts:10`).
- **Deployment and inferencing:** `frinksedge-deployment-backend`: `src/service/pythonModel.ts:200, 298, 360, 934-936`; `src/moduleTree/manager.ts:7, 324-350`; `src/dao/mattingConfig.ts`. `frinksedge-inferencing-backend`: `src/python-model/transformers/matting.transformer.ts`; `src/python-model/python-model-result.service.ts:21, 141, 186`; `src/shared/dto/dtos/parser.dto.ts:443-465, 951-958`; `src/shared/infrastructure/module-config/matting/` (feature name printed in database errors: `matting-config.repository.ts:6`); `src/shared/infrastructure/module/module.repository.ts:336-448` (a missed lookup falls back to the Search Area table at `:448`); `src/module-tree/module-tree.service.ts:308-326`.
- **AI engine:** `vip-inference-python`: `scripts/assembly/modules/moduleRegistry.py:30`, `modules/mattingModule.py`, `interfaces/mattingInterface.py:142-148, 243`, `resultProcess/matting.py:232-432`, `models/model_keys.py:31`, `models/ModelManager.py:973-988` (MODELLOAD_005), `components/main_utils.py:21-61` (model path setting), `interfaces/orientationInterface.py:174`, `modules/validation/module_validators.py:149`, `scripts/main.py:284-286`. Weights are loaded by layer name (`models/matting/matting.py:53`); `vitmatte/decoder/detail_capture.py:94, 136` (`matting_head`, `matting_convs`) must not be renamed.
- **AI engine images:** `Python-inference-docker`: `Dockerfile:139-145` downloads the model into `/app/mattingmodel`, and `vip-inference-python`: `Example.env:47-48` points the engine there (corrects the earlier note that this folder is unused). Not changed in this release.
- **Training, evaluation, recommender:** `train-utils`: `src/train_utils/io/downloader.py:175-191` (also committed `__pycache__` files). `workflow-dataprocessor`: `src/workflow_dataprocessor/modules/mattingModule.py`, `interfaces/mattingInterface.py:16-19, 125, 201`, `workflow/workflow_processor.py:947-1158`. `Segmentation-Training` (`master` and live `create_build_dev`): `{unet,pointrend}-segmentation/scripts/downloader.py:201-209`, `scripts/interface/mattingInterface.py:14-15, 122, 209`, `scripts/modules/mattingModule.py:8-59`. `Segmentation-Evaluation` (`master` and live `create_build_dev`): same layout. `OCR-Training-Detection-ALL`: `{CRAFT,psenet,Snake}/scripts/trainUtils/downloader.py:273-296`, `TRAINING.env` files (also a committed `Snake/scripts/.coverage`). Same model-download step and model settings on the live `create_build_dev` branches (not on their default branch) of: `Assembly-Training-OCR` (`scripts/downloader.py:225-227`, `TRAINING.env:75-76`, `scripts/.env`), `assembly-training` (`fasterrcnn/scripts/downloader.py:223`, `TRAINING.env:56-61` and per-model `.env`), `classification-training-docker` (`resnet18-cls/scripts/downloader.py:220-225`, `TRAINING.env:58-62`), `Assembly-model-evaluation` (`scripts/components/getConfig.py:529-534`, `EVALUATION.env:42-43`), `Classification-Evaluation` (`scripts/components/getConfig.py:259-264`, `EVALUATION.env:41-42`), `Craft-model-evaluation` (`scripts/components/getConfig.py:433-438`, `EVALUATION.env:43-55`, plus a copied ViTMatte folder), `OCR-Evaluation` (`scripts/components/getConfig.py:270-278` incl. the log line "Downloading matting model if exists...", `EVALUATION.env:45-51`). Recommenders: `scripts/components/getConfig.py` in `assembly-dataset-recommender` (348-352), `Segmentation-Data-Recommender` (325-329), `Recommendation-classification` (226-230), `Recommendation-OCR` (232-236), and their `.env` files.
- **Chat assistant manuals:** `frinkschat-backend`: `Frinks-Manuals-Query-Engine/Frinks Vision/manuals-text/Segmentation_Setup.md:58, 117, 144-145`, `Image_Annotation.md:78`, `reference/DICTIONARY.md:128`, `reference/ABBREVIATIONS.md:18`, `CLAUDE.md:38`.
- **Edge screens reading the type id:** `frinksedge-deployment-frontend`: `src/modules/inferencing-customizations/AnalysedImageConfigurations.jsx:126`.
- **Review notes:** the reviewer's `docs/mattingRename.md` (tested table-rename SQL, Part 1) and `docs/easyrename.md`.
- **Database scripts:** Vision changes run by hand from `frinksvision-backend/release-sql/<feature>/` (the backend never runs them); Edge schema lives in `frinksedge-deployment-backend/dump.sql`.

Repos checked (27 Oct 2026): frinksvision-frontend @ df358e8, frinksvision-backend @ 1703f24, frinksedge-deployment-backend @ 5a69c77, frinksedge-deployment-frontend @ 857dfddd, frinksedge-inferencing-backend @ 310c18e, frinksedge-report-frontend @ 9893a19, frinksedge-health @ 2dbc622, vip-inference-python @ 3af02b5, Python-inference-docker @ 62e22bd (50_series), workflow-dataprocessor @ d2cd9bd, train-utils @ d3e6378, OCR-Training-Detection-ALL @ fc59f48, assembly-dataset-recommender @ 0556f06, Segmentation-Data-Recommender @ 58d1e07, Recommendation-classification @ 0796dbb, Recommendation-OCR @ ae1e953, Segmentation-Training @ f749aab (+ create_build_dev), Segmentation-Evaluation @ 44bb8c6 (+ create_build_dev), Auto-annotation @ d213b71 (+ auto-segment 6e56c5f), frinkschat-backend @ 88e5c91, and the other 14 synced repos (no mention).

## Changelog
Every line names the US / AC ids it touched.
- 27 Oct 2026 — First draft, US1–US12 (AC1.1–AC12.9). Decisions: name Edge Refinement, prefix ER, internal name edgeRefinement; ViTMatte code left as-is; one release with no old-name support; database table renamed too; stakeholders Swayam (SDE) and Shreyas (AI); prototype not needed (Tanay Deo)
- 27 Oct 2026 — Duplicate pass: AC12.6 (removed): covered by AC6.2, which now names the schema-mismatch refusal; AC12.9 trimmed to the staging test (the one-release switch is AC6.2); AC11.5 trimmed (MODELLOAD_005 detail is AC5.4); AC11.1 trimmed (scripts bundle is AC11.3). Contradictions settled by earlier decisions: AC6.4 allows the image's unused model step until its next rebuild (default e) and covers shipped repos only (client repos not shipped) (Tanay Deo)
- 27 Oct 2026 — Approved. Owner set to Swayam (SDE) and Shreyas (AI); no AC changes (Tanay Deo)
- 28 Oct 2026 — Briefly split into phases (phase 1 = screens, inspect tools, logs); reverted the same day by the PM: "let's make tickets on everything - repo changes, table name changes, everything. only sde poc will work on this." Full scope restored as approved on 27 Oct, plus: AC6.5, AC6.6, AC6.7 added (Network on Vision and Edge, Console); G1 names the inspect tools; Appendix B adds the live create_build_dev branches of Assembly-Training-OCR, assembly-training, classification-training-docker, Assembly-model-evaluation, Classification-Evaluation, Craft-model-evaluation and OCR-Evaluation (covered by AC5.3, AC5.5, AC5.6, AC6.4); Owner and Stakeholders: Swayam (SDE POC) only. Approved (Tanay Deo)
- 28 Oct 2026 — Scope cut by the PM: "whatever error codes/logs are shown in the ui, they should not contain the word matting" and the Vision cloud job repos, chat manuals and staging notes dropped. US7 (removed); AC5.6 (removed); AC5.3 now covers job messages shown in Vision screens; AC5.5 Edge settings only; AC6.1, AC6.2 Vision keeps the old internal name towards the unchanged jobs; AC6.4 word search limited to Vision/Edge frontends, servers and Edge AI engine; back to In review (Tanay Deo)
- 28 Oct 2026 — Approved (scoped-down version); no AC changes. Tickets: Epic FP-3895, Story FP-3896, assigned to Deb (Tanay Deo)
- 28 Oct 2026 — Review round (FP-3895/FP-3896 review). PM decisions: drop what no user can see; rename existing MAT_ modules; chat manuals and the older-Edge guard: PM had no preference, recommended option applied. AC5.1, AC5.5, AC6.4 (removed); AC4.1 changed (no key contains MATTING; ER proved by a test); AC5.2 adds the Health database error tag; AC5.3 notes it holds today; AC6.1 Edge keeps the old name towards the AI engine; AC6.5, AC6.6 add exceptions for user-typed text and pre-release data, say "the word matting"; AC6.8 added (Sources); AC2.3 added (saved filters); AC11.2 corrected (the image's model folder IS used, Example.env:47-48); AC11.3 reworded; AC11.7 added (MariaDB 10.5.2); AC12.10 (MAT_ to ER_), AC12.11 (older-Edge download guard), AC12.12 (re-deploy after upgrade) added; US13 added (chat manuals back; US7 number not reused); US4 Teams SDE; back to In review (Tanay Deo)
- 28 Oct 2026 — Duplicate pass on the review round: AC5.3 (removed): covered by AC6.5, which now names job messages and chat answers; AC6.5, AC6.6 narrowed to data requests (Fetch/XHR, WebSocket) so AC6.8 alone checks the loaded code; AC13.1 trimmed (the 'never Matting' clause is AC6.5); AC4.1 trimmed to the ER test (the no-MATTING clause is AC6.6). Contradictions fixed: AC6.1, AC6.2 limited so the AI engine keeps its internal name; AC6.7 gets the AC6.5 exceptions; AC12.7 now points existing modules at the new entry; AC12.10 is Vision-only; Out of scope no longer says variables keep the old name (Tanay Deo)
- 28 Oct 2026 — Approved (review-round version); no AC changes. Tickets synced: Epic FP-3895, Story FP-3896 (Tanay Deo)
- 29 Oct 2026 — PM decision: no Matting modules exist in the Vision production database, so existing Vision modules need no handling. AC12.2 (removed); AC12.10 (removed); Summary and the Recipe editor row of the impact map no longer mention renaming existing MAT_ modules. AC12.1 (entry and settings table rename) and AC12.11 (older-Edge download guard, which protects modules made after the release) unchanged. Timeline line added (SPRINT 26-15, where the tickets sit). Approved with this change at the PM's instruction (Tanay Deo)

## Checked, not affected
- vision-projects, vision-variants: the module has no project or variant setting.
- mod-segmentation: the module's only parent; nothing in Segmentation itself changes (its auto-annotation toggle is under vision-auto-annotation).
- mod-bead-inspection, mod-classification, mod-code-reader, mod-color-detection, mod-input-master-image, mod-ocr, mod-odc, mod-search-area, mod-tracker: never linked to this module.
- edge-emc: the module is not in Edit Module Configurations.
- edge-live-dashboard: shows widgets bound to keys; key names are covered under Dashboard Builder (US4), and no site has a dashboard bound to this module.
- edge-storage-image-saving: no saved image or folder is named after the module on Edge.
- edge-access-audit, edge-camera-capture, edge-encryption-keys, edge-installer, edge-networking, edge-plc-setup, edge-system-time: no link to the module or its name.
- Code back-check: mod-bead-inspection, mod-classification, mod-code-reader, mod-input-master-image, mod-ocr, mod-odc, mod-search-area, mod-segmentation, mod-tracker, vision-projects, vision-variants, edge-access-audit, edge-camera-capture, edge-emc, edge-encryption-keys and edge-storage-image-saving share files with this change (the module, instance and result services, the Edge database script, the engine's model loader), but only the lines for this module change there.
