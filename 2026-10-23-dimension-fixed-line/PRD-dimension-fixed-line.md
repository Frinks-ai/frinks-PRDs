# PRD: Dimensioning — distance and angle to a fixed line

**Prototype:** [Interactive workflow, Today vs After change](https://vbv-fr.github.io/dimension-fixed-line-prototype/)
**Product:** Frinks Edge
**Status:** Approved
**Date:** 24 October 2026
**Owner:** Tanay Deo
**Related:** FP-3228 Dimension Configuration (Edge), FP-3404 Canvas for Dimension Configuration, FP-3456 Capture Layout, FP-3230 Keys for Shapes and Dimensions (all Done); PRD "Module Outputs in Reports & Analytics" (In review: its setup window lists every single-capture dimension's keys, fixed-line dimensions included, with no change there)
**Code checked:** 23 October 2026 — 34 repos at their latest default-branch commit (list in Appendix B)

---

## Discussion points
None. Every decision is made and written into the user stories.

## 1. Summary
- On Edge's **Dimension Configuration** page, a Distance's Feature B can now be a **Fixed line** that the user types in, instead of a shape.
- Three kinds of line: **Horizontal (y =)**, **Vertical (x =)** and **Tilted** (a centre point and an angle).
- Angle dimensions get a new mode, **From a fixed line**, with the same three kinds.
- While the dimension is being set up, the preview canvas draws the line across the whole image and shows the measured span. A live cursor read-out helps the user find the numbers to type.
- Values are typed in camera pixels, counted from the image's top-left corner. The line stays fixed to the camera image on every cycle, wherever the part sits.
- The distance is the shortest distance from Feature A's point to the line. It is always positive and is judged against Ground Truth like any other distance, in px or mm.
- Fixed lines also work in cross-capture distances, measured through Capture Layout.
- The analysed image draws the line, except for cross-capture dimensions.
- No role or permission changes, no Vision changes, and no Docker image or installer change.

## 2. Why we are making this
- **Today:** A Distance needs two detected features: two points, or a point and a detected line. An integrator who must check how far a hole, edge or peak sits from a fixed position in the image (a fixture line, a datum row, a slanted reference) has nothing to pick, because no shape exists there. The workarounds are adding a dummy Shape Extractor shape on a fixed edge, which breaks when the edge isn't visible, or skipping the check.
- **Evidence:** Product's own idea: a gap in Dimensioning found while reviewing the module.
- **After:** The user types the reference line, sees it on the image straight away, and gets an OK/NG distance or angle against it on every cycle.

## 3. Goals and non-goals
**Goals**
- G1. Measure the distance from any Feature A point to a typed-in horizontal, vertical or tilted line, in one capture or across two captures.
- G2. Measure the angle between a Feature A line and a typed-in line.
- G3. Show the line on the setup canvas while it is being typed, and on the analysed image for single-capture dimensions.
- G4. Fixed-line dimensions save, list, deploy and report like every other dimension, and nothing that works today changes.

**Non-goals**
- A line that moves with the part.
- Placing the line by dragging or clicking on the canvas.
- Telling which side of the line a point is on.
- Any change in Frinks Vision.

## 4. Key concepts
- **Fixed line:** a reference line the user types in on the Dimension Configuration page. It is horizontal (y = value), vertical (x = value) or tilted (centre x, centre y and an angle), and it has no length: it runs across the whole image.
- **Camera pixels:** positions in the capture's full-size image, counted from the top-left corner, with y growing downward. The preview canvas shows a smaller copy of the image but works in camera pixels.

## 5. Impact map
The parts this changes. Parts checked and not affected are listed at the very end.

| Part | What will happen | User stories |
|---|---|---|
| Shape Extractor (mod-shape-extractor) | Dimensions no longer need a second shape: Feature B can be a typed-in line, shown in the table as "Fixed line · …" | US1, US2, US4, US5, US9 |
| Edit Module Configurations and Dimension Configuration page (edge-emc) | The Distance form gets the Shape \| Fixed line switch, the Angle form gets "From a fixed line", and the canvas draws the line with a cursor read-out | US1, US2, US3, US5, US8, US9 |
| Master image (mod-input-master-image) | The canvas places the typed camera-pixel values on the smaller preview copy of the master image | US2 |
| Result module (mod-result) | The engine measures and judges point-to-line distances and line-to-line angles against the fixed line, and draws the line on the analysed image | US3, US6, US7, US10 |
| Search Area (mod-search-area) | No change to areas. Fixed lines are measured in the full camera image, whatever area Feature A's shape sits under | US6 |
| Orientator and Orientation & Tilt (mod-orientation) | No change. Datum alignment does not move a fixed line | US6 |
| Cycles and result storage (edge-inferencing-customization) | Cross-capture fixed-line distances are worked out at cycle close through Capture Layout, and no result image draws their line | US4, US10 |
| Deployments (edge-deployment) | The deploy check counts fixed-line dimensions as configured and never blocks them for a missing Feature B shape | US5, US9 |
| Instance upload (edge-instance-details) | A re-upload keeps fixed-line dimensions. A changed image size does not flag them | US5, US10, US12 |
| Edge access & audit (edge-access-audit) | Same Inferencing Customization – Update permission and the same audit entries | US8 |
| Rules and actions (edge-actions) | The same five dimension keys drive rules | US10 |
| PLC Write (edge-plc-write) | The same keys can be written to the PLC | US10 |
| Internal actions (edge-internal-actions) | The same keys drive internal actions | US10 |
| Live Dashboard (edge-live-dashboard) | The analysed image shown live carries the fixed line | US7 |
| Image saving (edge-storage-image-saving) | Saved analysed images carry the fixed line | US7 |
| Reports & Analytics (edge-reports-analytics) | Each cycle's stored result includes fixed-line dimensions | US10 |
| Model loading and variant change (edge-model-loading-variant-change) | After a changeover, the new variant's fixed-line dimensions are measured | US10 |
| Backup and migration (edge-backup-migration) | New stored fields arrive on install and update. Backups keep them. Existing dimensions are untouched | US12 |
| Release and upgrades (edge-release-upgrade) | Ships in the scripts bundle. Rolling back makes fixed-line dimensions errored | US11, US12 |
| Python runtime image (engine-runtime-packaging) | No change: no new library | US11 |
| Installer (edge-installer) | No change: the new fields ride in the database script the installer already runs | US11 |
| Health and error codes (edge-health) | No new error codes | US11 |
| Instance creation (vision-instance-creation) | No change: instance packages carry no dimensions | US12 |

## 6. User stories and acceptance criteria

### US1 — Measure a distance to a fixed line
*As an integrator, I want to measure the distance from a feature to a line I type in, so that I can check where the feature sits against a fixed position in the camera image.*
- **Kind:** Feature
- **Parts:** mod-shape-extractor, edge-emc
- **Teams:** SDE
- **Covers:** C3, C4
- **Today:** A Distance's Feature B must be a point or line on a Shape Extractor shape (Capture → Shape → Part → Point). There is no way to type a line in.
- **Prototype:** [Distance to a fixed line](https://vbv-fr.github.io/dimension-fixed-line-prototype/#edge-dim-distance)

![Today: Distance with Feature B picked on a shape](prototype/dimension-config-distance-today.png)
![After: Distance with Feature B as a horizontal fixed line](prototype/dimension-config-distance-after.png)

- **AC1.1** In a Distance dimension, the Feature B block starts with a "Shape | Fixed line" switch. On Shape, it shows today's Capture, Shape, Part and Point pickers unchanged.
- **AC1.2** On Fixed line, Feature B shows Capture (set to Feature A's capture), then the choice "Horizontal (y =) | Vertical (x =) | Tilted", then the boxes for that choice: "y" in px for Horizontal, "x" in px for Vertical, and "Centre x" px, "Centre y" px and "Angle" ° for Tilted.
- **AC1.3** Picking Fixed line starts on Horizontal, with y on the middle row of the capture's image (540 on a 1080-row image).
- **AC1.4** While Feature B is a fixed line, the Distance axis field is hidden.
- **AC1.5** y, x, Centre x and Centre y take up to 2 decimals and must lie inside the capture's image: y and Centre y from 0 to the image height, x and Centre x from 0 to the image width. After a Save attempt, a value outside these limits shows "Enter a value between 0 and <limit> px." under its box, and the dimension is not saved.
- **AC1.6** Angle takes 0° to 180°, counted counter-clockwise from horizontal as seen on screen. After a Save attempt, a value outside this range shows "Enter an angle between 0° and 180°." and the dimension is not saved.
- **AC1.7** Saving a Distance with a fixed-line Feature B succeeds. Opening it again with Edit shows Fixed line with the same line type and values.
- **AC1.8** Edge's server refuses a fixed-line dimension with a missing or out-of-range value even when it is sent without the page, and stores nothing.
- **AC1.9** Changing Feature B from Fixed line to Shape, or back, and saving keeps only the chosen kind. Switching to Fixed line later starts from the defaults in AC1.3.

### US2 — See the fixed line on the canvas while setting it up
*As an integrator, I want to see the fixed line on the master image as I type it, so that I place it where I mean to without trial deployments.*
- **Kind:** Feature
- **Parts:** mod-shape-extractor, edge-emc, mod-input-master-image
- **Teams:** SDE
- **Covers:** C4
- **Today:** The canvas draws only shapes and the span between two picked features. There is no cursor position read-out anywhere on the page.
- **Prototype:** [Distance to a fixed line](https://vbv-fr.github.io/dimension-fixed-line-prototype/#edge-dim-distance)

![After: vertical fixed line across the full image height](prototype/dimension-config-distance-vertical-after.png)
![After: tilted fixed line edge to edge of the image](prototype/dimension-config-distance-tilted-after.png)

- **AC2.1** On a single-capture dimension, the preview canvas draws the fixed line dashed, in the dimension's OK colour: across the full image width for Horizontal, the full image height for Vertical, and from image edge to image edge for Tilted. It never draws a short segment.
- **AC2.2** The line moves on the canvas as each value is typed, before saving.
- **AC2.3** The canvas draws the span from Feature A's point to the fixed line solid, with today's end markers and a right-angle mark where it meets the line.
- **AC2.4** Moving the cursor over the image shows its position as "x <n> · y <n> px", in camera pixels, at the bottom-left of the canvas.
- **AC2.5** At every zoom level, a line typed at y = 540 sits exactly on row 540 of the camera image.

### US3 — Measure an angle from a fixed line
*As an integrator, I want to measure the angle of a detected edge against a line I type in, so that I can check tilt against a reference that is not horizontal or vertical.*
- **Kind:** Feature
- **Parts:** edge-emc, mod-result
- **Teams:** SDE + AI
- **Stakeholders:** Tanay (SDE), Pallavi (AI, Python)
- **Covers:** C4, C9
- **Today:** Angle measures Feature A's line against the X axis, the Y axis, or a second shape's line. There is no tilted or positioned reference.
- **Prototype:** [Angle from a fixed line](https://vbv-fr.github.io/dimension-fixed-line-prototype/#edge-dim-angle)

![Today: Angle from X axis](prototype/dimension-config-angle-today.png)
![After: Angle from a tilted fixed line](prototype/dimension-config-angle-after.png)

- **AC3.1** The Angle mode list reads From X axis, From Y axis, From a fixed line, Two features.
- **AC3.2** From a fixed line shows the same "Horizontal (y =) | Vertical (x =) | Tilted" choice, boxes and value limits as a Distance (AC1.2, AC1.5, AC1.6). It has no capture picker: the line sits on Feature A's capture.
- **AC3.3** The canvas draws the fixed line as in AC2.1, plus an arc for the opening between Feature A's line and the fixed line.
- **AC3.4** The result is the angle between Feature A's line and the fixed line, from 0° to 180°, like other Angle results.

### US4 — Use a fixed line in a cross-capture distance
*As an integrator, I want to measure from a feature on one camera to a fixed line on another camera's image, so that I can check positions across a part that no single camera sees whole.*
- **Kind:** Feature
- **Parts:** mod-shape-extractor, edge-inferencing-customization
- **Teams:** SDE
- **Covers:** C2, C4, C9, C12
- **Today:** A cross-capture Distance joins a point on one capture to a point on another, always as a straight mm distance across the Capture Layout.
- **Prototype:** [Cross-capture fixed line](https://vbv-fr.github.io/dimension-fixed-line-prototype/#edge-dim-cross)

![Today: cross-capture distance between two shape points](prototype/dimension-config-cross-today.png)
![After: cross-capture distance to a fixed line on Side Camera](prototype/dimension-config-cross-after.png)

- **AC4.1** Picking a capture other than Feature A's for a Fixed line makes the dimension cross-capture, with the "⇄ CROSS-CAPTURE" badge.
- **AC4.2** The value is the shortest distance in mm from Feature A's point to the line, after both captures are placed by Capture Layout using each capture's pixel-to-mm, orientation and position.
- **AC4.3** Saving needs Capture Layout for both captures, with today's message "Configure Capture Layout (Pixel to mm) for … before saving".
- **AC4.4** The cross-capture preview draws the line across the whole layout stage, at its place on Feature B's capture image.
- **AC4.5** No result image draws the fixed line of a cross-capture dimension.

### US5 — See fixed-line dimensions in the table and deploy them
*As an integrator, I want fixed-line dimensions listed clearly and accepted by the deploy check, so that I can review them and run the line.*
- **Kind:** Feature
- **Parts:** mod-shape-extractor, edge-emc, edge-deployment, edge-instance-details
- **Teams:** SDE
- **Covers:** C5, C6, C10
- **Today:** The table shows Feature B as "Shape · part · point". A Distance with no Feature B shape is marked errored, and the deploy check blocks it.
- **Prototype:** [Saved dimensions](https://vbv-fr.github.io/dimension-fixed-line-prototype/#edge-dim-saved)

![Today: dimensions table](prototype/dimension-config-list-today.png)
![After: table with fixed-line rows](prototype/dimension-config-saved-after.png)

- **AC5.1** The Feature B cell reads "Fixed line · y = 540 px", "Fixed line · x = 960 px" or "Fixed line · Tilted (960, 540) at 30°". On a cross-capture row the capture name comes first, for example "Side Camera · Fixed line · x = 960 px".
- **AC5.2** A fixed-line dimension is never marked errored for having no Feature B shape: no red "errored" badge, no list banner and no form banner.
- **AC5.3** On Deployments, a variant whose dimensions use fixed lines counts as configured, and its fixed-line dimensions never block Next for a missing Feature B shape.
- **AC5.4** If a later instance upload changes the capture's image size so that a saved line falls outside the image, the dimension keeps measuring and is not marked errored. Its values are checked against the new size only when it is next saved.

### US6 — Measure and judge against the fixed line on the line
*As a quality engineer, I want every cycle measured against the same fixed line, so that the OK/NG verdict reflects where the part really sits.*
- **Kind:** Feature
- **Parts:** mod-result, mod-search-area, mod-orientation
- **Teams:** SDE + AI
- **Stakeholders:** Tanay (SDE), Pallavi (AI, Python)
- **Covers:** C9
- **Today:** The engine measures distances only between two shape features, always as a positive number, in the full camera image.

- **AC6.1** On every image, the engine measures the shortest distance from Feature A's point to the fixed line in camera pixels of the full image. It then applies the pixel-to-mm ratio, the unit and the Offset exactly as for other distances.
- **AC6.2** The line stays at the same place in the camera image on every cycle, whichever search area, Orientator or datum alignment Feature A's shape sits under. For a horizontal line, a part sitting 20 px lower, and staying on the same side of the line, gives a value 20 px different.
- **AC6.3** The value is always positive: a point 120 px above the line and a point 120 px below it give the same value.
- **AC6.4** A fixed-line dimension whose value falls outside its Ground Truth Min–Max fails, and the capture's verdict is NG.

### US7 — See the fixed line on the analysed image
*As an operator or quality engineer, I want the analysed image to show the fixed line and the measured span, so that I can see at a glance why a part passed or failed.*
- **Kind:** Feature
- **Parts:** mod-result, edge-live-dashboard, edge-storage-image-saving
- **Teams:** AI
- **Covers:** C12
- **Today:** The analysed image draws a distance between two shape features, with the value label, in the OK or NG colour.
- **Prototype:** [Analysed image: OK](https://vbv-fr.github.io/dimension-fixed-line-prototype/#edge-analysed)

![Today: analysed image, distance from a hole to a detected edge](prototype/analysed-image-ok-today.png)
![After: analysed image, distance from a hole to a fixed line, OK](prototype/analysed-image-ok-after.png)
![After: analysed image, fixed line, NG](prototype/analysed-image-ng-after.png)
![After: analysed image, angle from a tilted fixed line](prototype/analysed-image-angle-after.png)

- **AC7.1** For a single-capture dimension, the analysed image draws the fixed line dashed across the whole image (edge to edge for a tilted line), in the OK colour when the dimension passes and the NG colour when it fails.
- **AC7.2** For a single-capture dimension, the analysed image draws the span from Feature A's point to the line with today's dots, a right-angle mark where it meets the line, and today's value label (for example "6.00mm").
- **AC7.3** For an Angle from a fixed line, the analysed image draws the line across the image, an arc at the crossing for the measured opening (never the larger opposite side), and the value in degrees.
- **AC7.4** The Live Dashboard image and saved analysed images show the same drawing as the analysed image.

### US8 — Rules
*As a plant admin, I want fixed-line dimensions governed by the same permission as every dimension, so that access stays as I set it.*
- **Kind:** Rules
- **Parts:** edge-access-audit, edge-emc
- **Teams:** SDE
- **Covers:** C1

- **AC8.1** No role or permission changes. Adding, editing and deleting any dimension, fixed-line ones included, needs Inferencing Customization – Update. Viewing the page stays open to everyone who can open it today.
- **AC8.2** Edge's server refuses a fixed-line dimension save from a user without Inferencing Customization – Update.
- **AC8.3** Adding, editing and deleting a fixed-line dimension writes "dimension added", "dimension updated" or "dimension deleted" to the audit log, as for any dimension.

### US9 — Keeps working: setting up dimensions
*As an integrator, I want every dimension I set up today to behave as before, so that the new option costs me nothing.*
- **Kind:** Keeps working
- **Parts:** mod-shape-extractor, edge-emc, edge-deployment
- **Teams:** SDE + AI
- **Stakeholders:** Tanay (SDE), Pallavi (AI, Python)
- **Covers:** C3, C5, C10, C11

- **AC9.1** Shape-based Feature B options (points, "Perpendicular on the line", "Perpendicular from Previous Line") measure and draw on the canvas as today, and the Angle modes From X axis, From Y axis and Two features look and measure exactly as today.
- **AC9.2** If Feature A's shape is deleted, a fixed-line dimension shows as errored and blocks deployment, as any dimension with a missing shape does today.
- **AC9.3** Saving while a deployment is running shows today's confirmation, and the change reaches the line only after a deploy or Restart All.
- **AC9.4** A fixed-line dimension is deleted from the table's Actions menu like any dimension.
- **AC9.5** The Shape Extractor editor canvas in Edit Module Configurations, which shares the image viewer with the Dimension Configuration canvas, looks and works as today, with no cursor read-out added.

### US10 — Keeps working: on the line
*As a quality engineer, I want fixed-line dimensions to flow through keys, reports and changeovers like any dimension, so that nothing downstream needs special handling.*
- **Kind:** Keeps working
- **Parts:** mod-result, edge-inferencing-customization, edge-actions, edge-plc-write, edge-internal-actions, edge-reports-analytics, edge-model-loading-variant-change, edge-instance-details
- **Teams:** SDE + AI
- **Stakeholders:** Tanay (SDE), Pallavi (AI, Python)
- **Covers:** C2, C9, C10, C11, C12

- **AC10.1** When Feature A's shape is not found, or its fit fails, on an image, a fixed-line dimension fails with no value, as other dimensions do.
- **AC10.2** Cross-capture distances between two shape features stay a straight mm distance, as today.
- **AC10.3** Fixed-line dimensions write the same five dimension keys (result, predicted value, unit used, process, current ground truth). PLC Write, Internal Actions and dashboard rules built on those keys keep working.
- **AC10.4** A cross-capture fixed-line dimension that can't be computed in a cycle is FAIL when the cycle closes, like other cross-capture dimensions.
- **AC10.5** Each cycle's stored result in Reports & Analytics includes fixed-line dimensions like any dimension.
- **AC10.6** After a variant change, the new variant's fixed-line dimensions are the ones measured.
- **AC10.7** Re-uploading the instance from Vision keeps the fixed-line dimensions of every capture the re-upload keeps unchanged.
- **AC10.8** A capture removed by a re-upload takes its fixed-line dimensions out of the table, like any dimension on that capture.

### US11 — Platform
*As a release engineer, I want to know exactly what ships, so that the release is packaged and documented right.*
- **Kind:** Platform
- **Parts:** edge-release-upgrade, engine-runtime-packaging, edge-installer, edge-health
- **Teams:** SDE + AI
- **Stakeholders:** Tanay (SDE), Pallavi (AI, Python)
- **Covers:** C13, C17, C18

- **AC11.1** Node Docker image: no change. The work is code inside existing Edge services.
- **AC11.2** Python Docker image: no change. The measuring and drawing use libraries the engine already has.
- **AC11.3** Scripts bundle: changes. It carries the Dimension Configuration page, the Edge deployment server (save, list and deploy check), the Edge inferencing server (what is sent to the engine, and the cross-capture maths) and the Python engine (measuring and drawing).
- **AC11.4** Installer: no change. The new stored fields ship in the release's database script.
- **AC11.5** New error codes: none. A fixed line has no detection that can fail, and the existing engine and cross-capture error codes still apply.
- **AC11.6** The Edge manual gets a Dimension Configuration section that covers fixed lines (there is none today). The release note reads: "Dimensioning on Edge can now measure a distance or an angle against a fixed line you type in (horizontal, vertical or tilted), shown live on the setup canvas and on the analysed image of single-capture dimensions. Before rolling an Edge back to an older version, delete its fixed-line dimensions."

### US12 — Migration requirements
*As a site engineer, I want updates and rollbacks to leave existing dimensions intact, so that upgrading Edge is safe.*
- **Kind:** Migration
- **Parts:** edge-backup-migration, edge-release-upgrade, edge-instance-details, vision-instance-creation
- **Teams:** SDE
- **Covers:** C6, C7

- **AC12.1** Existing data: after an Edge upgrade, every existing dimension opens, shows and measures exactly as before, with Feature B on Shape.
- **AC12.2** The new fixed-line fields exist after both a fresh install and an update. A backup restored on another box keeps fixed-line dimensions unchanged.
- **AC12.3** Fixed lines can be added to the variants of an older instance, one uploaded before this release.
- **AC12.4** A new instance on an older Edge is unaffected: instance packages from Vision carry no dimensions, and Vision does not change.
- **AC12.5** After an Edge is rolled back from this release to an older Edge version, its fixed-line dimensions show as errored and block deployment until they are deleted.

## 7. Out of scope
- Signed distance, i.e. telling which side of the line a point is on.
- Placing or moving the line by dragging or clicking on the canvas.
- A line that moves with the part (set relative to a search area or the datum).
- Typing the line in millimetres.
- Fixed lines in Shape Features, Convexity and Contour Continuity.
- Drawing the fixed line on cross-capture result images.
- Any change in Frinks Vision.

## 8. How we'll know it worked
| Measure | Target |
|---|---|
| Deploys wrongly blocked by a fixed-line dimension | None reported after release |
| The line on the setup canvas and on the analysed image sit on the same pixel row or column | Same place on every test image |
| Dummy Shape Extractor shapes added only to act as a reference line, on sites that upgrade | Replaced by fixed lines on the next site visit |

## Appendix A — Coverage
**Not applicable:** every checklist item no story covers, one line each.
- **C8** — N/A: one point-to-line calculation per dimension per image, with no measurable effect on cycle time, GPU or memory.
- **C14** — N/A: no datasets, annotation, training or models are involved.
- **C15** — N/A: no network or clock dependence; works offline as today.
- **C16** — N/A: no licensing or encryption change, and nothing new leaves the plant.

**Coverage check** (24 Oct 2026, final draft): **PASS**, no warnings. 12 user stories (7 Feature, 1 Rules, 2 Keeps working, 1 Platform, 1 Migration), 62 ACs, 23 touched parts, 0 discussion points. Checklist: 14 items covered by stories, 4 N/A, 0 open.

**Checks beyond the script:** every coverage-sheet item maps to exactly one AC; the duplicate pass found 5 duplicates and 6 wording conflicts, all fixed (see Changelog); the code back-check added AC9.5; every goal is delivered (G1: US1, US4, US6; G2: US3; G3: US2, US7; G4: US5, US9, US10).

## Appendix B — Code references
For developers. Per touched part: the files that hold today's behaviour, from this sync.
- **Dimension Configuration page (edge-emc, mod-shape-extractor):** `frinksedge-deployment-frontend`:
  - `src/modules/inferencing-customizations/DimensionConfigurationPage.jsx:333-461` — list and form views; errored banner `:367-376`; Add/Edit/Delete gated `:386,399,410-411`.
  - `.../dimension-configuration/DimensionForm.jsx:282-510` — form fields; errored banner `:268-279`.
  - `.../dimension-configuration/FeaturePicker.jsx:94-160` — Capture → Shape → Part → Point.
  - `.../dimension-configuration/dimensionConstants.js:81-97` — angle modes, distance axis, derivations; `:518-535` Feature B perpendicular entries; `:747-757` needsFeatureB / canCrossCapture.
  - `.../dimension-configuration/dimensionFormModel.js:96-139` (row → form), `:326-331` (cross-capture rule), `:372-508` (validateForm), `:527-592` (buildPayload).
  - `.../dimension-configuration/dimensionGeometry.js:35-39` (angle convention), `:56-64` (perpendicularFoot), `:407-467` (resolveDistanceSpan).
  - `.../dimension-configuration/DimensionPreviewCanvas.jsx:227-319` (distance and angle drawing), `:445-625` (cross-capture stage).
  - `.../dimension-configuration/dimensionDisplay.js:50-92` (table cells).
  - `src/shared/ui/MasterImageCanvas.jsx:16-18,69-236` (pixel overlays, pan/zoom).
- **Save, list, deploy check (mod-shape-extractor, edge-deployment, edge-access-audit):** `frinksedge-deployment-backend`:
  - `src/dto/dimensioningConfig.ts:27-84,154-157` — create/update contract.
  - `src/service/dimensioningConfig.ts:36-82` (normalizeTypeSpecificFields), `:88-103,327` (Feature B always loaded as a shape), `:156-289` (validateFeature, validateDerivation), `:321-368` (cross-capture and ratio rules), `:403-431` (create mapping).
  - `src/dao/dimensioningConfig.ts:50-121` (column lists, splitFields), `:123-195` (listByVariantId, isErrored), `:329-538` (readiness SQL; Feature B null check `:477-505`).
  - `src/service/deployment.ts:260-285,927-933`; `src/controller/deployment.ts:26-58` — deploy check.
  - `src/db/dimensioningConfig.schema.ts:66-118`, `src/db/dimensioningFeatureReference.schema.ts:40-108` — tables and delete rules.
  - `src/routes/dimensioningConfig.ts:8-13`; `src/rbac/catalogue.ts:126-136`; `src/audit/catalogue.ts:43-45`; `src/controller/dimensioningConfig.ts:32-76` — permission and audit.
  - `src/db/cameraConfig.schema.ts:61-62` — capture width/height; `src/service/instance.ts:149,200-205` — preview master image downscaled to 1080 px.
  - `src/service/instanceUploadOwnership.ts:1-10` — dimensions are Edge-owned; `src/service/shapeExtractorUploadReconciliation.ts:255-331` — re-upload.
- **Engine payload and cross-capture (mod-result, edge-inferencing-customization):** `frinksedge-inferencing-backend`:
  - `src/python-model/python-model-dimensions.service.ts:86-103,137-234` — payload; FeatureB only when a shape is set `:166-190`; target area list `:215-220`.
  - `src/shared/infrastructure/module-config/dimensioning/dimensioningFeatureReference.schema.ts:42-47` — its own schema copy.
  - `src/cycle/cycle-manager.service.ts:725-742,849-891`; `src/dimensioning/cross-capture-geometry.ts:303-354` — cross-capture maths and verdict.
  - `src/key/key-writer/system-key-writer/system-key-writer.service.ts:142-207`; `src/key/keys/system-key.ts:241-245` — dimension keys.
  - `src/result-flusher/result-flush.service.ts:75-97` — stored result.
- **Measuring and drawing (mod-result, mod-search-area, mod-orientation):** `vip-inference-python`:
  - `scripts/assembly/resultProcess/dimension.py:49-107` (frames), `:264-640` (process, failures), `:185-241,503-549` (pixel-to-mm, unit, offset).
  - `scripts/assembly/resultProcess/dimensioning/distance.py:203-266,403-426` — point-to-line foot on the infinite line, absolute values.
  - `scripts/assembly/resultProcess/dimensioning/angle.py:57-78,146-234` — axis angle, 0–180°.
  - `scripts/assembly/resultProcess/dimensioning/utils/visualization.py:116-410` — drawing.
  - `scripts/assembly/modules/searchAreaModule.py:146-151,771-833` — root area identity matrix, datum alignment.
  - `scripts/assembly/modules/resultModule.py:1010-1026,1250-1256` — verdict.
- **Install and upgrade (edge-backup-migration, edge-installer):** `frinksedge-deployment-backend` `dump.sql:993,1005,1044` (recent dimension columns), `src/db/dataMigrations/index.ts:34-39`; `frinksedge-installer-electron` `src/main.js:265,347-401,512`.

Repos checked: frinksedge-deployment-frontend @ 3c027014 (2026-10-23), frinksedge-deployment-backend @ be6d0aa (2026-10-22), frinksedge-inferencing-backend @ 310c18e (2026-10-22), vip-inference-python @ 93451d1 (2026-10-15), frinksedge-report-frontend @ 9893a19 (2026-10-13), frinksedge-installer-electron @ b4a5049 (2026-10-12), frinksvision-backend @ d812aa7 (2026-10-23), frinkschat-backend @ 88e5c91 (2026-07-21); the other 26 repos were synced and not read.

## Changelog
Every line names the US / AC ids it touched.
- 24 Oct 2026 — Draft written: US1–US12 (Tanay Deo)
- 24 Oct 2026 — AC9.5 added at the code back-check: the Shape Extractor editor canvas shares the image viewer. Duplicate pass: AC2.5 trimmed (engine row is AC6.1); AC9.1 trimmed (Shape pickers are AC1.1); AC11.4 trimmed (install and update are AC12.2); AC11.5 trimmed (missing Feature A shape is AC10.1); AC12.3 trimmed (existing dimensions are AC12.1). Wording conflicts settled: AC2.1, AC7.2 and the AC11.6 release note limited to single-capture (cross-capture is AC4.4, AC4.5); AC5.3 limited to a missing Feature B shape (AC9.2); AC6.2 limited to the same side of the line (AC6.3); AC10.7 limited to kept captures (AC10.8). (Tanay Deo)
- 26 Oct 2026 — Status Draft → Approved; no US or AC changed (Tanay Deo)

## Checked, not affected
Every other part in the component map, grouped, each group with a one-line reason.
- vision-access, vision-auto-annotation, vision-captures-cameras, vision-datasets-annotations, vision-model-evaluation, vision-model-training, vision-projects, vision-recipe-tree, vision-training-sets-recommender, vision-variants: dimensioning exists only on Edge, and nothing about it travels from Vision.
- mod-bead-inspection, mod-classification, mod-code-reader, mod-color-detection, mod-masking, mod-matting, mod-ocr, mod-odc, mod-segmentation, mod-tracker: these modules produce no dimension features.
- engine-workflow: runs the result step and hands on the analysed image as today, with no change of its own.
- edge-camera-capture, edge-dashboard-builder, edge-encryption-keys, edge-networking, edge-plc-read, edge-plc-setup, edge-system-time: no new hardware, key, widget option, service, port, PLC address or time handling.
