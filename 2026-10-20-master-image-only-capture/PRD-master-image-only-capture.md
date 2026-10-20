# PRD: Master-image-only captures

**Product:** Vision + Edge
**Status:** Approved
**Date:** 20 October 2026
**Owner:** Tanay Deo
**Prototype:** [All screens, Today vs After change](https://vbv-fr.github.io/master-image-only-prototype/)
**Related:** FP-3487 (skip steps 3 and 4 when nothing is trainable), FP-3325 (deployment block), FP-3444 and FP-3741 (image saving and raw images in Reports), FP-3712 and FP-3714 (Recipe flow Part A and C, in flight on the same screens)
**Code checked:** 20 October 2026 — 34 repos at their latest default-branch commit (list in Appendix B)

---

## 1. Summary
- A capture can now run with **only its master image** and no inspection modules.
- In Recipe Management, a tree that holds only the master image shows **Finish** instead of Next. One click completes the capture.
- On Edge, **every image of that capture is OK**. A cycle that mixes it with normal captures is decided by the normal captures alone.
- These captures can be mixed freely with normal captures in any variant or instance, or make up a whole instance.
- Their images are **saved, shown on the dashboard and reported** like any passing capture. The analysed image is the full frame with only the green border and "Input Image" label of a passing search area; the raw image is the clean frame.
- PLC and dashboard rules can use their results, and they never cause extra model reloads.

## 2. Why we are making this
- **Today:** some lines need a camera position that is part of the cycle but needs no inspection, or a picture of every part for traceability. Vision won't let such a capture be finished: Next is greyed out with no reason when the tree holds only the master image. Teams work around it with a dummy module. On Edge, such a capture already gives OK, but its images never reach Reports or the dashboard, and a cycle made only of these captures shows "N/A" images and a wrong "Fail".
- **Evidence:** field need from a running deployment (a filler capture inside a multi-capture cycle, plus an image record of every part).
- **After:** integrators finish these captures in one click. On the line they always pass, their images are kept, and they never slow the line down.

## 3. Goals and non-goals
**Goals**
- G1. Finish a capture that has only the master image, in one click.
- G2. Every image of such a capture is OK, and cycles are decided by the other captures.
- G3. Their images are saved, shown and reported like any passing capture.
- G4. They work everywhere on Edge: mixed instances, all model-loading modes, rules and changeover.

**Non-goals**
- Any inspection on these captures, such as comparing each image with the master image.
- Changes to Datasets & Annotations, training or the Data Recommender.
- Fixing older Edge versions already in the field.

## 4. Key concepts
- **Master-image-only capture:** a capture whose recipe tree holds only the master image.
- **Master image search area:** the full-image search area ("Input Image") that comes with every capture's master image. It is the only search area these captures have.
- **Filler capture:** a master-image-only capture used only so that a multi-capture cycle completes.

## 5. Impact map
The parts this changes. Parts checked and not affected are listed at the very end.

| Part | What will happen | User stories |
|---|---|---|
| Recipe tree (vision-recipe-tree) | With only the master image in the tree, the step shows Finish, and one click completes the capture | US1, US11 |
| Master image node (mod-input-master-image) | Finish needs an uploaded master image; its search area is the capture's only area | US1, US14 |
| Recipe Management table (vision-captures-cameras) | Module Info reads "Master image only", and Configuration Status is green Complete | US2 |
| Instance creation (vision-instance-creation) | These captures can be ticked, and the package lets Edge save, show and report the master image search area | US3, US14, US16 |
| Instance upload on Edge (edge-instance-details) | Edge accepts projects that have no classes | US3, US16 |
| Result module (mod-result) | The master image search area's result is OK on every image | US4, US12 |
| Python engine (engine-workflow) | The engine treats the master image search area as an output area: OK result, and an analysed image with the green "Input Image" border | US4, US7, US15 |
| Cycles and result storage (edge-inferencing-customization) | Edge stores a record of every image of these captures; a cycle still needs every capture to report | US8, US12, US13, US15 |
| Model loading (edge-model-loading-variant-change) | Images of these captures leave the loaded models in memory | US5, US14 |
| Rules and actions (edge-actions) | The master image search area's key holds the result (always OK here) on every image, so PLC and dashboard rules using it work | US6, US12 |
| Image saving (edge-storage-image-saving) | Raw and Analysed saving both work for these captures, and the saved files are recorded | US7, US14 |
| Reports & Analytics (edge-reports-analytics) | Each of these captures gets its own Pass row with its images, and all exports include it | US8, US14 |
| Live Dashboard (edge-live-dashboard) | The master image search area tile shows the live image with the green "Input Image" border of a passing search area | US9 |
| Dashboard Builder (edge-dashboard-builder) | The image picker offers the master image search area | US9 |
| Edit Module Configurations (edge-emc) | Shows "No inspection modules in this capture." instead of an empty, greyed-out list | US10 |

## 6. User stories and acceptance criteria

### US1 — Finish a capture that has only the master image
*As an integrator, I want to finish a capture that has only its master image, so that I can add filler or image-record captures without dummy modules.*
- **Kind:** Feature
- **Parts:** vision-recipe-tree, mod-input-master-image
- **Teams:** SDE
- **Covers:** C3, C4, C5, C14
- **Today:** Next is greyed out with no reason when the tree holds only the master image, so the capture can never be completed.
- **Prototype:** [Recipe step](https://vbv-fr.github.io/master-image-only-prototype/recipe-step.html)

![Today: only the master image in the tree, and Next is greyed out](prototype/recipe-step-today.png)
![After: Finish is shown and can be clicked](prototype/recipe-step-after-finish.png)
![After: no master image yet, so Finish is greyed out with "Upload a master image first."](prototype/recipe-step-after-no-image.png)
![After: an unconfigured module was added, so Next is greyed out and names it](prototype/recipe-step-after-unconfigured.png)

- **AC1.1** With a master image uploaded and no other module in the tree, the Inspection Workflow step shows Finish instead of Next.
- **AC1.2** Clicking Finish marks the capture Complete in one click. Dataset Folders, Sample Images and Sample Labels are skipped, and no dataset folders are created.
- **AC1.3** Until a master image is uploaded, Finish is greyed out with the tooltip "Upload a master image first."
- **AC1.4** When a module is added but not configured, the button becomes a greyed-out Next whose tooltip names the unfinished module.
- **AC1.5** Removing every module from a capture brings back Finish, and the capture's existing dataset folders and labels stay as they were.
- **AC1.6** Adding a module to a finished master-image-only capture returns it to in-progress until the module is configured. Instances already downloaded are unchanged.
- **AC1.7** The master image node can't be deleted: the screen offers no Delete, and the server refuses the request with an error.
- **AC1.8** If Finish fails to save, the capture stays in progress, an error message shows, and the tree is unchanged.

### US2 — Recognise these captures in Recipe Management
*As an integrator, I want Recipe Management to show which captures have only a master image, so that I don't mistake them for unfinished work.*
- **Kind:** Feature
- **Parts:** vision-captures-cameras
- **Teams:** SDE
- **Covers:** C3
- **Today:** Module Info says "No modules added yet!" and the status stays red at step 1.
- **Prototype:** [Recipe Management](https://vbv-fr.github.io/master-image-only-prototype/recipe-management.html)

![Today: "No modules added yet!" and a red step-1 status](prototype/recipe-management-today.png)
![After: "Master image only" and green Complete](prototype/recipe-management-after.png)

- **AC2.1** A finished master-image-only capture shows "Master image only" in Module Info.
- **AC2.2** Its Configuration Status shows green Complete, with the tooltip "No inspection modules. Every image is marked OK."

### US3 — Put these captures in an instance and load it on Edge
*As an integrator, I want to include these captures in an instance, alone or with normal captures, so that they run on the line.*
- **Kind:** Feature
- **Parts:** vision-instance-creation, edge-instance-details
- **Teams:** SDE
- **Covers:** C2, C10, C12
- **Today:** these captures can't be ticked, because they never become Complete. Edge refuses an upload from a project with no classes.
- **Prototype:** [Instance captures](https://vbv-fr.github.io/master-image-only-prototype/instance-captures.html)

![Today: the master-image-only capture is greyed out in the wizard](prototype/instance-captures-today.png)
![After: it can be ticked like any Complete capture](prototype/instance-captures-after.png)

- **AC3.1** A finished master-image-only capture can be ticked in the instance wizard, together with normal captures or on its own.
- **AC3.2** After upload, Analysed Image can be switched on for these captures in Image Saving Configurations, and their master image search area appears on the default dashboard and in Reports.
- **AC3.3** Edge accepts the upload of an instance whose project has no classes.
- **AC3.4** An instance made only of these captures deploys without error.

### US4 — Always OK
*As an operator, I want these captures to always pass, so that the part's result depends only on the real inspections.*
- **Kind:** Feature
- **Parts:** mod-result, engine-workflow
- **Teams:** AI
- **Covers:** C9
- **Today:** the capture already gives OK, but its master image search area has no result of its own.

- **AC4.1** Every image of a master-image-only capture gives OK for the capture.
- **AC4.2** Every image also gives OK for the capture's master image search area.

### US5 — No extra model loading
*As a plant admin, I want filler captures to cost no model reloads, so that the line keeps its cycle time.*
- **Kind:** Feature
- **Parts:** edge-model-loading-variant-change
- **Teams:** AI
- **Covers:** C8, C10
- **Today:** in loading mode 3 (and mode 2 when the capture has its own capture position), an image of this capture unloads the instance's models, and the next capture reloads them on every cycle.

- **AC5.1** In every model-loading mode, an image of a master-image-only capture leaves the loaded models in memory, and the next capture that uses them doesn't reload them.

### US6 — Use these captures in rules
*As an integrator, I want PLC and dashboard rules to use these captures' results, so that I can signal the machine as with any capture.*
- **Kind:** Feature
- **Parts:** edge-actions
- **Teams:** SDE
- **Covers:** C9
- **Today:** PLC Read, PLC Write, Internal Action and dashboard-button rules offer the master image search area's key, but it never holds a value, so a rule using it makes the deploy fail.
- **Prototype:** None — the rule screens look the same; only the value behind the key changes.

- **AC6.1** The master image search area's key holds the result (always OK) on every image, and an instance with a rule on that key deploys without error and runs the rule.

### US7 — Save these captures' images
*As a quality engineer, I want each image of these captures saved, so that I have a picture of every part.*
- **Kind:** Feature
- **Parts:** edge-storage-image-saving, engine-workflow
- **Teams:** SDE + AI
- **Stakeholders:** Tanay (SDE), Pallavi (AI, Python)
- **Covers:** C8, C12
- **Today:** the Analysed Image switch is stuck off ("No result config was included while creating project"). Raw images can be saved, but Edge never records where.
- **Prototype:** [Image saving](https://vbv-fr.github.io/master-image-only-prototype/image-saving.html)

![Today: Analysed Image can't be switched on](prototype/image-saving-today.png)
![After: Raw Image and Analysed Image both work](prototype/image-saving-after.png)

- **AC7.1** In Image Saving Configurations, the capture's row lets both Analysed Image and Raw Image be switched on, with OK/NG, Resolution and Quality, like any capture.
- **AC7.2** With Analysed Image on, every image is saved as the full frame with a green border and the label "Input Image" around it, exactly as for any passing search area, and nothing else drawn, in the master image search area's OK folder.
- **AC7.3** With Raw Image on, every image is saved in the raw OK folder, and Reports can open it.
- **AC7.4** Saving the images of these captures takes no longer per image than saving a raw image of a normal capture does today.

### US8 — See these captures in Reports
*As a quality engineer, I want these captures in Reports like any passing capture, so that I can trace every part with its images.*
- **Kind:** Feature
- **Parts:** edge-reports-analytics, edge-inferencing-customization
- **Teams:** SDE
- **Covers:** C12
- **Today:** Edge stores nothing for these captures. In a mixed cycle they have no row. A cycle made only of them shows "N/A" images and "Fail" under Config Result, though the cycle passed.
- **Prototype:** [Reports](https://vbv-fr.github.io/master-image-only-prototype/reports.html)

![Today: no row for Top View; the Pack Photo cycle shows N/A and Fail](prototype/reports-today.png)
![After: every master-image-only capture has its own Pass row with its images](prototype/reports-after.png)
![After: View Images on Top View shows the frame with the green Input Image border](prototype/reports-viewer-after.png)

- **AC8.1** Edge stores one record for every image of these captures, with Pass, the saved image locations and the inference time.
- **AC8.2** In a mixed cycle, each of these captures has its own row with Pass under Config Result, and no special marking.
- **AC8.3** View Images for that row opens the analysed image (the frame with the green "Input Image" border), and View Raw Images opens the clean frame.
- **AC8.4** A cycle made only of these captures shows their rows with Pass and their images, not "N/A", and Config Result shows Pass.
- **AC8.5** The Excel export, the old Excel export, the images zip and Reverse Judgement include these captures and their images, like any passing capture.

### US9 — See these captures on the dashboard
*As an operator, I want to see the live image of these captures with its result, so that the screen looks the same for every camera.*
- **Kind:** Feature
- **Parts:** edge-live-dashboard, edge-dashboard-builder
- **Teams:** SDE + AI
- **Stakeholders:** Tanay (SDE), Pallavi (AI, Python)
- **Covers:** C12
- **Today:** the default dashboard gives the master image search area a tile, but no image ever arrives, so it stays an empty box. Dashboard Builder can't pick it.
- **Prototype:** [Live Dashboard](https://vbv-fr.github.io/master-image-only-prototype/live-dashboard.html), [Dashboard Builder](https://vbv-fr.github.io/master-image-only-prototype/dashboard-builder.html)

![Today: the Top View tile is an empty box](prototype/live-dashboard-today.png)
![After: the Top View tile shows the live image with the green Input Image border](prototype/live-dashboard-after.png)
![After: Dashboard Builder offers Top View's master image search area](prototype/dashboard-builder-after.png)

- **AC9.1** On the default dashboard, the master image search area tile of these captures shows the live image with the green "Input Image" border, the same way every passing search area is shown.
- **AC9.2** In Dashboard Builder, the image component's picker offers the master image search area of these captures.

### US10 — Clear message in Edit Module Configurations
*As an integrator, I want EMC to tell me these captures have nothing to edit, so that I don't think the screen is broken.*
- **Kind:** Feature
- **Parts:** edge-emc
- **Teams:** SDE
- **Covers:** C11
- **Today:** the capture is listed, but the Module list is greyed out with nothing below it and no message.
- **Prototype:** [EMC](https://vbv-fr.github.io/master-image-only-prototype/emc.html)

![Today: a greyed-out Module list and an empty panel](prototype/emc-today.png)
![After: "No inspection modules in this capture."](prototype/emc-after.png)

- **AC10.1** Selecting one of these captures in EMC shows "No inspection modules in this capture." in the settings area, instead of an empty, greyed-out module list.

### US11 — Who can do this
*As an Org Admin, I want this to follow today's permissions, so that no one gets new access by accident.*
- **Kind:** Rules
- **Parts:** vision-recipe-tree
- **Teams:** SDE
- **Covers:** C1

- **AC11.1** Only users who can edit recipes in Recipe Management today can finish a master-image-only capture. On Edge, the same users as today can change Image Saving Configurations, rules and dashboards.
- **AC11.2** No role or permission is added, removed or changed, in Vision or on Edge.

### US12 — Cycle results keep working as today
*As an operator, I want cycle results to keep their meaning, so that the reject signal stays right.*
- **Kind:** Keeps working
- **Parts:** edge-inferencing-customization, mod-result, edge-actions
- **Teams:** SDE
- **Covers:** C2, C9

- **AC12.1** In a cycle with these captures and normal captures, the cycle is OK when all normal captures pass, and NG when any normal capture fails.
- **AC12.2** A cycle made only of these captures ends OK once all of them have reported.
- **AC12.3** The capture-level result, completion and count keys work as today, and the cycle-level PLC write fires at cycle close as today.

### US13 — A capture that never reports still ends the cycle NG
*As an operator, I want a cycle to end NG when a capture's image never arrives, so that a missing picture is never passed.*
- **Kind:** Keeps working
- **Parts:** edge-inferencing-customization
- **Teams:** SDE
- **Covers:** C9

- **AC13.1** If a master-image-only capture (like any capture) doesn't report within the cycle time, the cycle ends NG at the timeout, as today.

### US14 — Everything around these captures keeps working
*As an integrator, I want the rest of the flow to behave as it does today, so that nothing else changes for me.*
- **Kind:** Keeps working
- **Parts:** mod-input-master-image, vision-instance-creation, edge-model-loading-variant-change, edge-storage-image-saving, edge-reports-analytics
- **Teams:** SDE + AI
- **Stakeholders:** Tanay (SDE), Pallavi (AI, Python)
- **Covers:** C3, C4, C10, C12, C14

- **AC14.1** Deleting a capture's master image still removes every module under it and deactivates the project's instances, as today.
- **AC14.2** Model Selection is skipped when no ticked capture needs a model, and shows no row for these captures in a mixed instance.
- **AC14.3** Camera details are still required for every capture position in the instance wizard.
- **AC14.4** Changing to or from a variant that contains these captures completes within the existing 20-second changeover limit.
- **AC14.5** Saved images of these captures count against the storage limits and are cleaned up like any other saved image.
- **AC14.6** The Total, Pass, Fail and Defect % counters in Reports count cycles exactly as today.

### US15 — Platform requirements
*As a Frinks support engineer, I want to know exactly what ships, so that installs and upgrades go as planned.*
- **Kind:** Platform
- **Parts:** engine-workflow, edge-inferencing-customization
- **Teams:** SDE + AI
- **Stakeholders:** Tanay (SDE), Pallavi (AI, Python)
- **Covers:** C13, C16, C17, C18

- **AC15.1** Scripts bundle: changes. It carries the Edge backend, frontend and Python engine changes of this PRD. Vision ships through its normal cloud release.
- **AC15.2** Node Docker image: no change. No new libraries are needed.
- **AC15.3** Python Docker image: no change. No new libraries are needed, and every released Python scripts version already starts an instance with no models.
- **AC15.4** Installer: no change.
- **AC15.5** Error codes: none new. The remaining failures are covered by existing codes, and the refused delete in AC1.7 is a Vision error message.
- **AC15.6** The Vision Recipe Management manual and the Edge Analytics and Reports, Image Saving, Dashboard and Edit Module Configurations sections are updated, and the release note reads: "Captures can now run with only a master image: every image is OK, and its images are saved, shown and reported like any other capture."

### US16 — Migration requirements
*As a Frinks support engineer, I want existing data and boxes to keep working, so that upgrading is safe.*
- **Kind:** Migration
- **Parts:** vision-instance-creation, edge-instance-details
- **Teams:** SDE
- **Covers:** C6, C7

- **AC16.1** Existing data: no new tables or columns, so no database migration. Existing captures behave as before, and captures already holding only a master image can be finished after the release.
- **AC16.2** Older Edge with a new instance: it still gives OK for these captures, but shows no images in Reports or on the dashboard, a rule on the master image search area's key fails the deploy, and projects with no classes are refused. The release note states that the Edge release this ships in is the minimum version.
- **AC16.3** Older instance on the new Edge: works as before.
- **AC16.4** Upgrading Edge boxes in the field needs no database change and no extra upgrade step.

## 7. Out of scope
- Any inspection on these captures, such as comparing with the master image.
- The instance wizard's separate list of "trainable" modules, and the other existing bugs found while checking the code. They're raised separately.
- Data lost from Reports during Edge updates (a separate bug).

## 8. Open questions
None.

## 9. How we'll know it worked
| Measure | Target |
|---|---|
| New instances that use a dummy module to make a filler capture pass | 0 |
| Support tickets about missing images for filler or image-record captures | 0 |
| Cycle time with a filler capture versus without, in loading mode 3 | No difference |

## Appendix A — Coverage
**Not applicable:**
- **C15** — N/A: no new network use, timestamps or offline behaviour.

**Coverage check:**

**Result:** PASS

- 16 user stories (10 feature, 1 rules, 3 keeps working, 1 platform, 1 migration), 52 ACs, 15 touched parts, 0 open questions.
- Checklist: 17 items covered by stories, 1 N/A, 0 open.
- Every punchlist item I1–I52 in notes.md maps to exactly one AC.

## Appendix B — Code references
For the ticket skill and developers. Paths are relative to each repo's `src` unless shown.
- **Recipe tree:** `frinksvision-frontend`: `modules/project/module-configuration/index.tsx:353` (Next blocked when `currentModules.length === 1`), `:989-991` (tooltip), `:1021-1038` (button); `moduleWorkflow/index.tsx:857-869` (tree hidden until master image); `moduleNode.tsx:107` (no Delete on root). `frinksvision-backend`: `service/cameraConfig.ts:409-414, 440-458` (finishWithoutLabelImages), `utils/module.ts:20-32` (trainable types), `dao/cameraConfig.ts:177-213` (step flags), `dao/module.ts:1495` (delete has no root guard), `dao/module.ts:597-610` (root Input Module created with the capture).
- **Master image node:** `frinksvision-backend`: `service/configuration.ts:341-360` (deleting the master image removes child modules and deactivates instances), `controller/instance.ts:680-714` (export adds master image files only where one exists).
- **Recipe Management table:** `frinksvision-backend`: `utils/module.ts:18, 40-45` (Module Info text). `frinksvision-frontend`: `project-configuration/index.tsx:244-289`, `shared/copy/tooltips.ts:82-83, 97-98`.
- **Instance creation:** `frinksvision-frontend`: `instances/steps/CameraConfig.tsx:311-316, 400-413`, `instances/steps/MapCameraIp.tsx:377-455`, `instances/index.tsx:186-213`, `ModelSelection.tsx:428-429, 774-787`, `instanceWizard.ts:8-21`. `frinksvision-backend`: `service/instance.ts:676-690, 783-800, 1096`, `service/packageManifest.ts:83-86`.
- **Instance upload on Edge:** `frinksedge-deployment-backend`: `dao/instances.ts:1036-1037` (class join is required although commented optional), `service/instance.ts:966-975, 1950`.
- **Result module and engine:** `vip-inference-python/scripts`: `assembly/modules/resultModule.py:366, 505-575, 772, 1256`, `assembly/modules/searchAreaModule.py:149-153`, `assembly/models/ModelManager.py:52-70` (no models → no model key needed), `components/mainProcessor.py:231-249`. `frinksedge-inferencing-backend`: `module.repository.ts:499-521`, `python-model-result.service.ts:146-160`.
- **Cycles and result storage:** `frinksedge-inferencing-backend`: `result-flush.service.ts:78-96` (rows only per module result), `parser.dto.ts:643-646`, `cycle-manager.service.ts:528-533, 668, 725-727, 765-768`, `orchestrator.service.ts:111-163`.
- **Model loading:** `vip-inference-python/scripts`: `components/mainProcessor_utils.py:321-414`.
- **Rules and actions:** `frinksedge-deployment-frontend`: `createKeys.ts:156, 256-290`. `frinksedge-inferencing-backend`: `key-populator.service.ts:282, 413`, `system-key-writer.service.ts:95-101`, `action-loader.service.ts:568-592`.
- **Image saving:** `frinksedge-deployment-frontend`: `ImageSavingConfigurations.jsx:111-127, 391-421, 488-522`. `frinksedge-deployment-backend`: `dao/cameraConfig.ts:745-767`, `service/imageSavingConfig.ts:36-46`.
- **Reports:** `frinksedge-report`: `analysis.service.ts:200, 486-531`, `analysis.repository.ts:15-30, 357`, `download.service.ts:287, 599-607`. `frinksedge-report-frontend`: `ResultTable.jsx:140, 166`. `frinksedge-deployment-backend`: `inferenceResultConfigWise.schema.ts:48-52` (no schema change needed).
- **Dashboards:** `frinksedge-deployment-backend`: `dao/instances.ts:1267-1310`, `service/dashboard.ts:297`, `service/module.ts:71-75`. `frinksedge-deployment-frontend`: `ImageComponent.jsx:643-656`.
- **EMC:** `frinksedge-deployment-frontend`: `inferencing-customizations/hooks/useModuleConfig.js:96-118`, `EditModuleConfigurations.jsx:505-510`.

Repos checked (20 October 2026): frinksvision-frontend @ 110a23c, frinksvision-backend @ eda944f, vip-inference-python @ 93451d1, frinksedge-deployment-backend @ 009fc11, frinksedge-deployment-frontend @ 7e92de34, frinksedge-inferencing-backend @ eddd8e5, frinksedge-report @ 5fe5390, frinksedge-report-frontend @ 9893a19, plus 26 more in `.frinks-sync.tsv`.

## Changelog
- 20 October 2026 — First draft from the PRD skill dry run.
- 20 October 2026 — Owner set; Reports prototype hosted on GitHub Pages.
- 20 October 2026 — Restructured: user stories are the single list of work (Rules, Keeps working, Platform and Migration stories added); Changes by part, Edge cases and Decisions sections removed; prototypes for every changed screen; Q1 closed (model-less instances already start), Q2 turned into AC7.4, Q3 answered in AC16.2.
- 20 October 2026 — OK is shown the way every passing search area shows it: a green border and the "Input Image" label on the analysed image (AC7.2, AC8.3, AC9.1).
- 20 October 2026 — Stakeholders for SDE + AI stories: Tanay (SDE), Pallavi (AI, Python).
- 20 October 2026 — Approved (Tanay Deo).

## Checked, not affected
- vision-projects, vision-access, vision-variants: permissions, projects and variants work as today.
- vision-datasets-annotations, vision-training-sets-recommender, vision-auto-annotation, vision-model-training, vision-model-evaluation: these captures have nothing to label or train. They get no dataset folders and don't appear in Datasets & Annotations, as today.
- mod-search-area, mod-odc, mod-tracker, mod-classification, mod-color-detection, mod-code-reader, mod-masking, mod-ocr, mod-segmentation, mod-bead-inspection, mod-shape-extractor, mod-matting, mod-orientation: no inspection module is used. The master image node is a search area, but the Search Area Allocator itself doesn't change.
- edge-deployment, engine-runtime-packaging, edge-camera-capture: the deploy checks, the inference image and the cameras already handle these captures.
- edge-plc-setup, edge-plc-read, edge-plc-write, edge-internal-actions: their screens are unchanged. The values they pick from are covered in US6.
- edge-health: no new ways to fail; existing error codes cover the remaining failures (US15).
- edge-backup-migration, edge-release-upgrade, edge-installer, edge-networking, edge-system-time, edge-encryption-keys, edge-access-audit: no new data, services, ports, keys or permissions.
