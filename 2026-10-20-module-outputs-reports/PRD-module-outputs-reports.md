# PRD: Module Outputs in Reports & Analytics

**Prototype:** [Interactive workflow: Reports & Analytics, Today vs After change](https://vbv-fr.github.io/module-outputs-prototype/)
**Product:** Frinks Edge
**Status:** In review
**Date:** 20 October 2026
**Owner:** Tanay Deo
**Related:** FP-528 (open epic: Analytics & Reports menu). Earlier Reports work: FP-3715 (Raw Images column, Download popup), FP-3622 (Cycle Complete column), FP-3727 (Write to DB custom key)
**Code checked:** 20 October 2026 — 34 repos at their latest default-branch commit (list in Appendix B)

---

## Discussion points
Everything still to be settled.

| # | Point to settle | Options on the table | Owner | Needed by |
|---|---|---|---|---|
| Q1 | After release, how will we judge success? For example, a plant admin sets up Module Outputs once, and support stops opening View JSON for customers. What numbers should we aim for in section 8? | A share of upgraded Edge boxes with Module Outputs set up within a set time; a drop in support requests that need View JSON; no open customer requests for module values | Tanay Deo | Before approval |
| Q3 | Some values a quality engineer would want have no key today: image classification confidence, OCR text per label, tilt angle, bead incorrect calipers and gap sizes, segmentation region counts (segmentation in normal mode has no keys at all). They are stored with each cycle. Add keys for them? | Add new keys in this release (more Edge work); leave them out of v1 and raise a follow-up | Tanay Deo | Before approval |

## 1. Summary
- Reports & Analytics gets a **Module Outputs** button. It opens a setup window for the selected instance, listing every capture (grouped by variant) and, for each module, its **keys exactly as Edge shows them today** in PLC Write and Dashboard Builder (for example `[cls] Screw > [key-num] current_count`).
- Ticking a key shows a **Display Name** box. The person types the name the value appears under on the Reports page, for example "Screw count" or "Serial No.".
- The window opens on the instance shown in the Reports table, with an **Instance** dropdown to set up any other instance on the box.
- Two options at the top, **Show in same column** and **Show in separate columns**, decide the layout. In the same column, the ticked keys share one Module Outputs column. In separate columns, each ticked key gets its own column, headed by its Display Name.
- After saving, a new **Module Outputs** column (or one column per key) shows the ticked keys on each capture's row as `Display Name: value` lines, such as `Screw count: 4` or `Serial No.: SN-48213`.
- When each capture finishes, Edge **saves a copy of every key of that capture** with the cycle, ticked or not. Reports shows those copies: each value is what the key held at that moment, the same value the dashboard and PLC saw. A key ticked later still shows values for every cycle since this release; older cycles show "—". Changing the setup changes what every cycle shows, straight away, with no redeploy.
- Every module type that has keys is covered: object detection, image classification, OCR, code reader, segmentation (single-class mode), bead inspection, colour detection, shape extractor, orientation & tilt check and single-capture dimensions.
- The column is searchable with the existing Search box and exported to Excel as one "Module Outputs" column.
- Saving the setup needs the existing **Update** permission on Analytics & Reports. Everyone who can view Reports sees the column.

## 2. Why we are making this
- **Today:** Reports & Analytics shows only Pass or Fail for each cycle and each capture. To see why a capture failed (how many screws were found, which class a slot got, what serial number or code was read), someone has to open View JSON. It shows raw ids, one cycle at a time. The Class Name and Class Count columns show object counts only, show nothing for image classification, and never show read text or codes. The only workaround is one Write to DB rule per value. That gives one value per cycle (not per capture), and only for cycles run after the rule was made. Quality engineers, plant admins and support teams are affected.
- **Evidence:** customer request, and parity with competing inspection systems that show tool results next to each part.
- **After:** someone sets up once, per instance, which module keys to show and under what Display Name. A "Module Outputs" column then shows them as readable pairs, such as "Screw count: 4" or "Serial No.: SN-48213", for each capture of every cycle, on screen and in the Excel export.

## 3. Goals and non-goals
**Goals**
- G1. Inside Reports & Analytics, choose which of each capture's module keys to show, and give each a Display Name.
- G2. Show the chosen values as name: value pairs in a new "Module Outputs" column, per capture, for every cycle, including cycles stored before the setup.
- G3. Cover every module type that has keys on Edge today.
- G4. The column works with the existing Search box and appears in the Excel export.

**Non-goals**
- New search or filter controls for module values. The existing Search box is enough.
- Fast Mode lines (Minute-wise), which store no per-capture results.
- Values a module does not output today: the winning class of single-class segmentation, the default text of conditional classification, bead width in pixels, per-caliper widths.
- New keys. Values with no key today stay out (see Q3).
- Any change to how OK or NG is decided, or to PLC and dashboard keys.

## 4. Key concepts
- **Key:** a named value Edge keeps for an instance, capture, module, class, search area or label, the same keys offered today in PLC Write, Internal Actions and Dashboard Builder (for example `current_count` of class Screw).
- **Display Name:** the name a ticked key's value appears under on the Reports page and in the Excel, typed by the person setting it up.
- **Key copy:** the value a key held when a capture finished, saved by Edge with that capture in the cycle. Reports shows key copies, never today's live value.
- **Module Outputs setup:** the per-instance choice of which keys show in Reports, their Display Names, and whether they share one column or get one column each.
- **Show in same column / Show in separate columns:** the per-instance layout. Same column puts every ticked key in one Module Outputs column as `Display Name: value` lines. Separate columns gives each ticked key its own column, headed by its Display Name.

## 5. Impact map
The parts this changes. Parts checked and not affected are listed at the very end.

| Part | What will happen | User stories |
|---|---|---|
| Reports & Analytics (edge-reports-analytics) | New Module Outputs button and setup window; new Module Outputs column on each capture row; new Edit Table option; new Excel column; the column is searchable | US1, US2, US4, US5, US8 |
| Edge access & audit (edge-access-audit) | Saving the setup needs Update on Analytics & Reports, checked by the report server; every save is written to the audit log | US7 |
| Object Detection (mod-odc) | Offers its module keys and, per output class, `result`, `current_count` and `count_till_now` | US3, US9 |
| Image Classification (mod-classification) | Offers its module keys, `detected_class (<search area>)` per area and `correct_class` | US3, US9 |
| OCR (mod-ocr) | Offers `detected_text`; in Read & Match also its module keys and each label's `labelName:<label>_…` keys | US3, US9 |
| Code Reader (mod-code-reader) | Offers `detected_string`; in Read & Match also its module keys and `gt_string` | US3, US9 |
| Segmentation (mod-segmentation) | Offers its keys only with single-class output on; in normal mode it has no keys and shows "No keys" | US3, US9 |
| Bead Inspection (mod-bead-inspection) | Offers its module keys, `caliper_pass_percentage`, `bead_class_name` and per-bead `bead:<bead>_…` keys | US3, US9 |
| Colour Detection (mod-color-detection) | Offers its module keys and per-colour `colorName:<colour>_…` keys | US3, US9 |
| Shape Extractor (mod-shape-extractor) | Offers per-shape `shape:<shape>_…` measurement keys; each single-capture dimension offers its five `dim_…` keys | US3, US9 |
| Orientation & Tilt (mod-orientation) | Offers its module keys (`result`, `pass_count`, `fail_count`) | US3, US9 |
| Tracker (mod-tracker) | Not offered: it has no keys and produces no result on Edge today | US3 |
| Matting (mod-matting) | Not offered: it has no keys | US3 |
| Instance upload on Edge (edge-instance-details) | A re-upload keeps the setup; removed captures, modules and classes drop out of it; deleting the instance deletes it | US6, US11 |
| Edit Module Configurations (edge-emc) | Edits keep the setup; the values offered follow the current module settings | US6 |
| Cycles and result storage (edge-inferencing-customization) | Saves a copy of every key of each capture with the cycle, as the capture finishes; a module that did not run is saved as "Not run" | US12, US9 |
| Python engine (engine-workflow) | No change: results are produced and sent exactly as today | US9 |
| Result module (mod-result) | No change: OK and NG are decided exactly as today | US9 |
| Actions (edge-actions) | Write to DB rules that copy values into Additional Info columns keep working | US8 |
| Deployments (edge-deployment) | Fast Mode deployments get no Module Outputs, as they store no per-capture results | US8 |
| Health Monitoring (edge-health) | Storage cleanup deletes key copies together with their capture rows, and counts them in the data limit | US12 |
| Backup and migration (edge-backup-migration) | The saved setup is created on upgrade and survives every later upgrade | US11, US12 |
| Release and upgrades (edge-release-upgrade) | Ships in the scripts bundle as a normal release, with one database addition | US10 |

## 6. User stories and acceptance criteria

### US1 — Open the Module Outputs setup
*As a quality engineer, I want one place in Reports to choose which module values to show, so that I don't have to open View JSON for every cycle.*
- **Kind:** Feature
- **Parts:** edge-reports-analytics
- **Teams:** SDE
- **Covers:** C2, C5, C10, C13
- **Today:** Reports has Edit Table, Download, Filter and Refresh buttons. Module values can only be read in View JSON, as raw ids, one cycle at a time.
- **Prototype:** [Step 2: Open Module Outputs](https://vbv-fr.github.io/module-outputs-prototype/#setup)

![Today: no way to choose module values](prototype/reports-today.png)
![After: the Module Outputs window, first time, nothing ticked](prototype/setup-empty.png)
![After: the Instance dropdown in the window's top pane](prototype/setup-instance.png)

- **AC1.1** On the Cycle-wise tab, a "Module Outputs" button sits to the left of the Edit Table, Download, Filter and Refresh buttons. It is not shown on the Minute-wise tab.
- **AC1.2** The button is enabled whether or not an instance is selected in the Reports filter bar.
- **AC1.3** Clicking it opens the "Module Outputs" window. Its top pane holds an "Instance" dropdown, set to the instance selected in the Reports filter bar, and the two layout options (US2). On the left, every capture of the instance is listed under its variant as "<capture position> · <capture name>", each with a count of its ticked values. On the right, the selected capture's modules are shown, one card each with the module name and type, listing every key it has (US3). Each capture's own keys come first, in a card headed by the capture.
- **AC1.4** A module with no keys (for example segmentation in normal mode) shows its card with "No keys". Modules that never have keys (US3, AC3.11) are not listed.
- **AC1.5** The setup can be opened and saved whether the instance is deployed, running or stopped. Saved changes show in Reports at once, with no redeploy.
- **AC1.6** If the setup cannot be loaded, the window shows "Module Outputs could not be loaded (RPT-MOUT-002)", and the table keeps working without the Module Outputs column.
- **AC1.7** The Instance dropdown lists every instance on the box. Choosing another instance shows its captures, keys and saved setup. Unsaved changes on each instance are kept while switching, until Save or Cancel. Save stores every instance changed in the window. If no instance is selected in the Reports filter bar, the dropdown shows "Select an instance" and the panes stay empty until one is chosen.

### US2 — Choose and name the values
*As a quality engineer, I want to tick the keys I care about and give each a Display Name my team knows, so that the report reads like our own checklist.*
- **Kind:** Feature
- **Parts:** edge-reports-analytics
- **Teams:** SDE
- **Covers:** C2, C3, C4, C5, C13
- **Today:** Keys can only be picked one at a time, in PLC Write, Internal Actions or Dashboard Builder. Nothing puts them in Reports.
- **Prototype:** [Step 3: Choose keys and Display Names](https://vbv-fr.github.io/module-outputs-prototype/#choose)

![After: detected_text and detected_string ticked, with Display Names Serial No. and QR code](prototype/setup.png)
![After: Show in separate columns selected](prototype/setup-separate.png)

- **AC2.1** The first time the setup is opened for an instance, nothing is ticked, and the table has no Module Outputs column.
- **AC2.2** Each key row shows a tick box and the key the way Edge's key picker shows it today: the class, search area or label chip where there is one, then a "key-bool", "key-str" or "key-num" chip and the key name exactly as Edge stores it (for example `[cls] Screw > [key-num] current_count`).
- **AC2.3** Ticking a key shows a "Display Name" box on that row; unticking hides it. The key's value appears on the Reports page and in the Excel under this name.
- **AC2.4** A Display Name is required for every ticked key, up to 40 characters, and unique within a capture. An empty box shows "Enter a display name" and a duplicate shows "Name already used in this capture" under the box. Save stays disabled until every error is fixed.
- **AC2.5** Save stores the setup, closes the window, shows "Module Outputs saved", and the table updates at once. Cancel or the close (×) button discards unsaved changes.
- **AC2.6** If saving fails, the window stays open with the person's choices and shows "Module Outputs could not be saved (RPT-MOUT-001)". The setup saved earlier is unchanged.
- **AC2.7** When two people change the setup at the same time, the last save wins.
- **AC2.8** The setup belongs to the instance and is shared by everyone on the box: anyone who opens Reports for that instance sees the same columns. Two instances of the same project keep separate setups.
- **AC2.9** The top pane has two radio buttons, "Show in same column" (selected by default) and "Show in separate columns". The choice is saved per instance with the setup and applies to the table, Edit Table, Search and the Excel (US4, US5).

### US3 — Values each module offers
*As a quality engineer, I want to see the same keys Edge already uses for PLC and dashboards, so that what I pick for Reports matches what the line already knows.*
- **Kind:** Feature
- **Parts:** mod-odc, mod-classification, mod-ocr, mod-code-reader, mod-segmentation, mod-bead-inspection, mod-color-detection, mod-shape-extractor, mod-orientation, mod-tracker, mod-matting
- **Teams:** SDE
- **Covers:** C2, C12
- **Today:** These keys are offered in PLC Write, Internal Actions and Dashboard Builder. Their per-cycle values sit inside each cycle's stored result, keyed by ids, visible only in View JSON.
- **Prototype:** [Step 4: Other module types](https://vbv-fr.github.io/module-outputs-prototype/#more-modules)

![After: capture, bead and colour keys for one capture](prototype/setup-more.png)
![After: shape, tilt and dimension keys for the same capture](prototype/setup-more-end.png)

The list is exactly the set of keys Edge creates for the instance today; a key shown in PLC Write for a module is shown here, and no other.
- **AC3.1** Each capture offers its own keys: `result`, `inference_time`, `avg_inference_time`, `pass_count`, `fail_count` and `completion_status`.
- **AC3.2** Object Detection & Classification offers `result`, `pass_count` and `fail_count`, and for each output class `result`, `current_count` and `count_till_now`.
- **AC3.3** Image Classification offers `result`, `pass_count`, `fail_count`, `detected_class (<search area>)` for each search area, and `correct_class`.
- **AC3.4** OCR offers `detected_text`. In Read & Match it also offers `result`, `pass_count`, `fail_count` and, for each label, `labelName:<label>_gt_text`, `labelName:<label>_result`, `labelName:<label>_pass_count` and `labelName:<label>_fail_count`.
- **AC3.5** Code Reader offers `detected_string`. In Read & Match it also offers `result`, `pass_count`, `fail_count` and `gt_string`.
- **AC3.6** Segmentation offers `result`, `pass_count`, `fail_count`, `detected_class` and `correct_class` only when its single-class output is on. In normal mode it has no keys and its card shows "No keys".
- **AC3.7** Bead Inspection offers `result`, `pass_count`, `fail_count`, `caliper_pass_percentage` and `bead_class_name`, and for each bead `bead:<bead>_result`, `_pass_count`, `_fail_count`, `_bead_width` and `_bead_gap/position`.
- **AC3.8** Colour Detection offers `result`, `pass_count` and `fail_count`, and for each colour `colorName:<colour>_detected_color`, `_result`, `_pass_count`, `_fail_count` and `_distribution_percentage`.
- **AC3.9** Shape Extractor offers, for each shape, the `shape:<shape>_…` keys of its shape type: radius, perimeter, area, circularity, convexity, contour_continuity, error_pct, angle, length, major_axis, minor_axis, line_1 to line_4, diagonal_1, diagonal_2 and sector_angle, as Edge registers them.
- **AC3.10** Orientation & Tilt Check offers `result`, `pass_count` and `fail_count`. Each dimension measured within one capture offers `dim_result`, `dim_predicted_value`, `dim_unit_used`, `dim_process` and `dim_current_gt`, in a card at the end of the capture it belongs to.
- **AC3.11** Tracker, Matting, Orientator, Masking, Search Area Allocator, the master image and dimensions measured across captures have no keys here and are not listed.
- **AC3.12** Values show as they would in the key: numbers with up to 2 decimals, `distribution_percentage` as a percentage, true/false keys as "OK" or "NG".

### US4 — See Module Outputs in the table
*As a quality engineer, I want each capture's chosen values on its row, so that I can see at a glance why a cycle failed or which part it was.*
- **Kind:** Feature
- **Parts:** edge-reports-analytics
- **Teams:** SDE
- **Covers:** C5, C6, C12, C16
- **Today:** Each capture row shows only Camera Position, Camera Configuration and Config Result (Pass or Fail). Class Name and Class Count show object counts only.
- **Prototype:** [Step 5: Module Outputs column](https://vbv-fr.github.io/module-outputs-prototype/#column), [Step 6: Separate columns](https://vbv-fr.github.io/module-outputs-prototype/#separate), [Step 7: Edit Table](https://vbv-fr.github.io/module-outputs-prototype/#edit-table), [Step 8: Search](https://vbv-fr.github.io/module-outputs-prototype/#search)

![Today: capture rows show only Pass or Fail](prototype/reports-today.png)
![After: the Module Outputs column with each capture's values](prototype/reports-after.png)
![After: Show in separate columns, one column per key headed by its Display Name](prototype/separate-after.png)
![After: Module Outputs in Edit Table, after Config Result](prototype/edit-table-after.png)
![After: in separate columns, Edit Table lists each key column](prototype/edit-table-separate.png)
![Today: searching a serial number finds nothing](prototype/search-today.png)
![After: searching a serial number finds its cycle](prototype/search-after.png)

- **AC4.1** After the first save, the ticked keys appear after Config Result, as the last per-capture column or columns, and Camera Position and Camera Configuration are turned on with them.
- **AC4.2** With "Show in same column", one "Module Outputs" column shows each capture's ticked keys, one per line, as "Display Name: value", in the order of the setup (module order, then key order).
- **AC4.3** Values are the key copies saved with each cycle (US12). A key ticked today shows values for every cycle since this release. Cycles stored before this release have no copies and show "—" for every key. Changing the setup changes what every cycle shows.
- **AC4.4** For a capture triggered more than once in a cycle, the values come from the latest attempt, the same attempt Config Result shows.
- **AC4.5** A value with nothing found (no text read, no code decoded) shows "—"; a count of zero shows "0". A key saved as "Not run" (US12) shows "Not run". A capture with nothing ticked shows "N/A". Every key, including running totals such as `pass_count` and settings such as `gt_string`, shows the copy saved when that capture finished, never today's value.
- **AC4.6** Once set up, Edit Table lists "Module Outputs" after Config Result, or, with "Show in separate columns", each key column by its Display Name. Unticking one hides it. While any of them is ticked, Camera Position and Camera Configuration cannot be turned off, as with Config Result today. Edit Table choices stay shared for the whole box, as today.
- **AC4.7** The existing Search box also matches text in the Module Outputs column or the key columns, the same way it matches the other visible columns on the page. No new search or filter controls are added.
- **AC4.8** Text read from parts (OCR text, codes) is shown exactly as read, as plain text, never as a link or formatted content.
- **AC4.9** With "Show in separate columns", there is no Module Outputs column. Each ticked key gets its own column after Config Result, headed by its Display Name, in setup order (capture order, then module, then key). A cell holds only the value. A capture row fills only its own keys' columns; the others stay blank. Keys from different captures with the same Display Name share one column, each capture row filling its own value.
- **AC4.10** When the key columns don't fit the page width, the table scrolls sideways, keeping today's header style.

### US5 — Module Outputs in the Excel export
*As a quality engineer, I want the same values in the Excel, so that I can share and analyse them outside Edge.*
- **Kind:** Feature
- **Parts:** edge-reports-analytics
- **Teams:** SDE
- **Covers:** C8, C12, C16
- **Today:** The Excel has Class Name and Class Count columns only. Image classification rows come out blank, and read text and codes are never exported.
- **Prototype:** [Step 9: Excel export](https://vbv-fr.github.io/module-outputs-prototype/#excel)

![Today: the Excel has no module values](prototype/excel-today.png)
![After: the Excel has a Module Outputs column](prototype/excel-after.png)
![After: with separate columns, the Excel has one column per key](prototype/excel-separate.png)

- **AC5.1** With "Show in same column" and the column visible, "Report (Excel Sheet)" gets a "Module Outputs" column after Config Result, with one cell per capture row holding the same "Display Name: value" lines as the screen, separated by line breaks.
- **AC5.2** When the column is hidden in Edit Table, it is not exported, as with every other column today.
- **AC5.5** With "Show in separate columns", the Excel gets one column per visible key column instead, after Config Result, headed by its Display Name, in the same order and with the same value-only cells as the screen.
- **AC5.3** Values are written as text. A value that starts with =, +, - or @ is never treated as a formula.
- **AC5.4** Export limits and filters are unchanged: up to 100,000 cycles still export with the column on, and the time, instance, variant, outcome, class and additional-info filters apply.

### US12 — Edge saves a copy of every key with each capture
*As a quality engineer, I want each cycle to keep the values its keys had at that moment, so that the report shows what the line actually saw, not what the keys say now.*
- **Kind:** Feature
- **Parts:** edge-inferencing-customization, edge-health, edge-backup-migration
- **Teams:** SDE
- **Covers:** C6, C8, C12
- **Today:** Keys hold only their latest value; nothing keeps a key's value per cycle. A module that does not run on an image leaves its keys holding the previous image's value.

- **AC12.1** When a capture's result arrives and its keys are updated, Edge saves the value of every key of that capture (capture, module, class, search area, label, colour, bead, shape and single-capture dimension keys) with that capture's row in the cycle, whether or not the key is ticked in Module Outputs.
- **AC12.2** A module that did not run on that image (its search area was not found) has its keys saved as "Not run", not the value left from the previous image.
- **AC12.3** Each attempt of a capture triggered more than once in a cycle saves its own copies.
- **AC12.4** Fast Mode deployments save no copies, as they store no capture rows.
- **AC12.5** Saving copies does not delay the capture result, PLC writes or dashboard updates.
- **AC12.6** Storage cleanup deletes key copies together with their capture rows, and counts them in the instance's data limit.

### US6 — The setup follows the instance
*As a plant admin, I want the setup to survive instance updates and Edge-side edits, so that I set it up once.*
- **Kind:** Feature
- **Parts:** edge-instance-details, edge-emc
- **Teams:** SDE
- **Covers:** C3, C11
- **Today:** No setup exists.

- **AC6.1** Re-uploading the instance from Vision keeps the setup, including Display Names.
- **AC6.2** Captures, modules, classes, search areas or labels removed by a re-upload drop out of the setup, and their keys stop showing. New ones appear unticked.
- **AC6.3** Saving changes in Edit Module Configurations (for example switching OCR between Read and Read & Match, or changing ground truth or thresholds) keeps the setup. The values offered follow the current module settings, and a ticked key that no longer exists (for example a label's keys after switching to Read) drops out with its Display Name.
- **AC6.4** Deleting the instance deletes its setup.

### US7 — Who can do this
*As a plant admin, I want only trusted people to change what Reports show, so that the shared view stays reliable.*
- **Kind:** Rules
- **Parts:** edge-access-audit, edge-reports-analytics
- **Teams:** SDE
- **Covers:** C1, C16
- **Prototype:** [Step 10: Without Update permission](https://vbv-fr.github.io/module-outputs-prototype/#no-permission)

![After: the Module Outputs button is disabled without Update permission](prototype/no-permission-after.png)

- **AC7.1** No role or permission changes. Saving the setup needs the existing Update level on Analytics & Reports, the same as Mark and Comment. Administrator always can.
- **AC7.2** Without Update, the Module Outputs button is disabled, with the hover text "Needs Update permission on Analytics & Reports". The column is still shown to everyone who can view Reports.
- **AC7.3** The report server refuses a save without Update, with the existing "Update on Analytics and Reports" message, even if the request does not come from the screen.
- **AC7.4** With Roles & Permissions switched off on the box, everyone can change the setup, as with Mark and Comment today.
- **AC7.5** Each save is written to the audit log as "Module Outputs updated", with the user, time and instance (shown as "Guest" when Roles & Permissions is off).
- **AC7.6** Viewing and exporting Module Outputs follow today's rules for viewing and exporting reports, which are open to everyone.

### US8 — Reports & Analytics keeps working as today
*As a quality engineer, I want everything I use in Reports today to stay as it is.*
- **Kind:** Keeps working
- **Parts:** edge-reports-analytics, edge-actions, edge-deployment, edge-health
- **Teams:** SDE
- **Covers:** C9, C12

- **AC8.1** Every existing column, including Class Name, Class Count, Config Result and the Additional Info columns, shows the same values as before.
- **AC8.2** View Images, View Raw Images, View JSON, Mark (Reverse Judgement) and Comment work as before.
- **AC8.3** Write to DB rules that copy module values into Additional Info columns keep working alongside Module Outputs.
- **AC8.4** The Minute-wise tab shows the same per-minute rows and totals as before for Fast Mode deployments.
- **AC8.5** Filters, the Download popup ("Report (Excel Sheet)" and "Images Only") and its size checks work as before.
- **AC8.6** (removed — storage cleanup of key copies is now AC12.6)

### US9 — Inspection results keep working as today
*As an integrator, I want inspection, verdicts and PLC signals untouched, so that the line runs exactly as before.*
- **Kind:** Keeps working
- **Parts:** edge-inferencing-customization, engine-workflow, mod-result, mod-odc, mod-classification, mod-ocr, mod-code-reader, mod-segmentation, mod-bead-inspection, mod-color-detection, mod-shape-extractor, mod-orientation
- **Teams:** SDE
- **Covers:** C8, C9

- **AC9.1** OK and NG for every capture and cycle, PLC writes and dashboard keys are unchanged.
- **AC9.2** Edge keeps storing each module's full result for every capture of every cycle, exactly as today, and View JSON keeps showing it.
- **AC9.3** (removed — the only added work per image is saving key copies, covered by AC12.5)

### US10 — Platform requirements
*As a Frinks support engineer, I want to know what ships and how, so that the release goes out like any other.*
- **Kind:** Platform
- **Parts:** edge-release-upgrade, edge-reports-analytics
- **Teams:** SDE
- **Covers:** C13, C17, C18

- **AC10.1** Node Docker image: no change. No new libraries are needed; the Excel export already has one.
- **AC10.2** Python Docker image: no change. The engine is not touched.
- **AC10.3** Scripts bundle: changes. It carries the Reports app (screen and server), the inference service change that saves key copies, and the Edge database additions that hold the setup and the key copies.
- **AC10.4** Installer: no change. Database additions already run on every install and update.
- **AC10.5** New error codes: RPT-MOUT-001 "Module Outputs could not be saved" and RPT-MOUT-002 "Module Outputs could not be loaded". The report server logs them and the window shows them. They are not added to the Health page.
- **AC10.6** The Edge manual's Analytics & Reports section describes the Module Outputs button, the setup window, the column and the Excel column, with new screenshots. Release note: "Reports & Analytics can now show chosen module keys, such as counts, classes, read text and codes, under your own Display Names in a new Module Outputs column, for past and new cycles."

### US11 — Migration requirements
*As a Frinks support engineer, I want upgrades to keep every box's data and setup, so that no plant loses its reports or has to set them up again.*
- **Kind:** Migration
- **Parts:** edge-backup-migration, edge-instance-details
- **Teams:** SDE
- **Covers:** C6, C7, C15

- **AC11.1** Existing data: after the upgrade every instance starts with no setup and no column. No stored result is changed or moved.
- **AC11.2** The saved setup survives restarts and every later Edge upgrade. The database update replayed at each upgrade must not erase or reset it.
- **AC11.3** An older Edge with a new instance: nothing changes, because the instance package is not touched. The new Edge with an older instance: Module Outputs offers that instance's values like any other.
- **AC11.4** Edge boxes in the field get it as a normal release with no data migration. It works offline, and timestamps are unchanged.

## 7. Out of scope
- New search or filter controls for module values.
- Values that have no key today, such as image classification confidence, OCR text per label, tilt angle, bead incorrect calipers and gap sizes, and segmentation region counts (see Q3).
- Dimensions measured across captures. They are never saved with the cycle today, so a follow-up must store them first.
- Tracker counts. The tracker produces no result on Edge today, so it needs its own fix first.
- Matting mask counts.
- Copying a setup from one variant or instance to another.
- Module Outputs on the Live Dashboard, PLC or Minute-wise reports.

## 8. How we'll know it worked
| Measure | Target |
|---|---|
| Edge boxes on this release with Module Outputs set up for at least one instance (from the audit log) | Q1 |
| Support requests that need someone to read View JSON for a customer | Q1 |
| Customer requests for module values in reports still open after release | None open |

## Appendix A — Coverage
**Not applicable:**
- **C14** — N/A: no change to datasets, annotation, training, evaluation or model selection; values come from results the line already produces.

**Coverage check:**

**Result:** PASS, 3 warnings

- 12 user stories (7 feature, 1 rules, 2 keeps working, 1 platform, 1 migration), 78 ACs (2 marked removed), 23 touched parts, 2 discussion points.
- Checklist: 17 items covered by stories, 1 N/A, 0 open.
- Warnings kept on purpose: AC8.4 and AC12.4 contain "Fast", the product name "Fast Mode"; US3 names keys such as `current_count`, which are the names users see in Edge's key picker, not code.
- Every punchlist item I1–I78 in notes.md maps to exactly one AC (I-items for AC8.6 and AC9.3 marked removed).

## Appendix B — Code references
For the ticket skill and developers. Paths are relative to each repo.
- **Reports & Analytics:** `frinksedge-report-frontend`: `src/modules/analytics/index.jsx:1201-1646` — toolbar, Edit Table dropdown (1431-1518), column locks (1123-1180), session save (689-719), browser-side Search (773-925), Write to DB columns (476-494); `src/modules/analytics/columns.js:2-144` — columns, defaults, new-column restore; `src/modules/analytics/ResultTable.jsx:186-319` — per-cycle and per-capture cells, Class Name/Count; `src/modules/analytics/DownloadModal.jsx`, `downloadRequest.js:29-64` — export columns.
- **Reports server:** `frinksedge-report`: `src/features/analysis/analysis.service.ts:458-501` — folds a capture's module rows into one (drops module id); `src/features/analysis/analysis.repository.ts:174-251, 397-425, 492-605` — class filter, View JSON, name lookups (captures, variants, classes, search areas, modules); `src/features/session/session.service.ts:5-21` — box-wide saved view (`app_config` type `report_analytics`); `src/features/download/excel/excel.service.ts:573-597, 977-1009` — Excel headers, class columns, dropped empty cycles; `src/features/download/download.service.ts:235, 450-469` — 100,000-cycle limit, old result shapes; `src/shared/error/error.internal-codes.ts:1-49` — RPT codes.
- **Access & audit:** `frinksedge-deployment-backend`: `src/rbac/catalogue.ts:219-231, 244-279` — `rep` R/U and role templates; `frinksedge-report`: `src/features/analysis/analysis.module.ts:25-34`, `src/shared/auth/auth.service.ts:66-83`, `require-permission.ts:15-19`, `auth.errors.ts:21` — Update check; `src/shared/audit/audit.service.ts:10-13, 48-59` — audit codes; `frinksedge-report-frontend`: `src/core/auth/AuthContext.jsx:60-70` — `can()`.
- **Stored results:** `frinksedge-inferencing-backend`: `src/result-flusher/result-flush.service.ts:75-97` — one row per capture × module, capture outcome; `src/shared/dto/dto.base.ts:14-17`, `src/shared/dto/dtos/parser.dto.ts:118-142, 241-307, 404-440` — fields kept per module; `src/cycle/cycle-manager.service.ts:569-579` — repeat captures (sequence).
- **Module results:** `vip-inference-python`: `scripts/assembly/resultProcess/assembly.py:180-396` (ODC), `classification.py:118-459` (IC), `ocr.py:962-1071` (OCR), `codeReader.py:124-239` (code reader), `segmentation.py:145-208` (segmentation), `beadInspection.py:571-726` (bead), `color.py:49-225` (colour), `shape.py:354-388` (shape), `dimension.py:618-636` (dimensions), `orientation.py:190-214` (tilt), `tracker.py:81-124` (tracker); `scripts/assembly/modules/resultModule.py:786-795, 911-918` — tracker skipped, module skipped when its search area is invalid.
- **Names on the box:** `frinksedge-deployment-backend` `dump.sql` tables `variants`, `capture_position`, `camera_configs`, `modules`, `class`, `search_area_config.output_name`, `ocr_config.info[].labelName`, `bead_inspection_config.name`, `shape_extractor_config.name`.
- **Instance upload:** `frinksedge-deployment-backend`: `src/service/instance.ts:908-950, 1411-1469, 1856-1869, 2230-2267, 2610-2693` — dropped captures, label merge, id-keeping upsert, delete, per-instance module settings reconcile (`module_class_roi_config` precedent).
- **Database and upgrades:** `frinksedge-deployment-backend`: `dump.sql:2823-2869` (per-instance × module table precedent), `dump.sql:2969-2974` (repeat-capture delete replayed at every update); `frinksedge-installer-electron`: `src/main.js:265, 387, 512` — dump replay with `--force`; `release-manager-backend`: `src/services/edgeSchemaRegistry.ts:11-13, 169-182` — schema registration on new tables.
- **Health cleanup:** `frinksedge-health`: `src/service/storageMonitor.ts:164-200, 595-686`.

Repos checked: frinksedge-report @ 5fe5390 (15 Oct 2026), frinksedge-report-frontend @ 9893a19 (13 Oct 2026), frinksedge-inferencing-backend @ eddd8e5 (19 Oct 2026), frinksedge-deployment-backend @ 009fc11 (19 Oct 2026), frinksedge-deployment-frontend @ 7e92de34 (19 Oct 2026), frinksedge-health @ e8ef112 (15 Oct 2026), vip-inference-python @ 93451d1 (15 Oct 2026), frinksvision-backend @ eda944f (14 Oct 2026), frinksvision-frontend @ 110a23c (19 Oct 2026), Python-inference-docker @ 62e22bd (29 May 2026), release-manager-backend @ b8016f7 (22 Sep 2026), and the other 23 repos in the skill's list, all up to date on 20 Oct 2026.

## Changelog
- 26 October 2026 — Reports show a copy of each key saved when the capture finished (new US12; AC4.3, AC4.5, AC9.2, AC10.3 changed; AC8.6 and AC9.3 removed). Every key of every capture is copied, ticked or not, so cycles since the release show values; older cycles show "—". Q2 (running totals) closed: they show the copy at that moment. Same Display Name across captures sharing a column, and columns for every variant's keys, confirmed (Tanay Deo)
- 24 October 2026 — Instance dropdown at the top of the setup window, defaulting to the instance in the Reports table (AC1.2 changed, AC1.7 added); "Show in same column / Show in separate columns" layout per instance (AC2.9, AC4.9, AC4.10, AC5.5 added; AC2.8, AC4.1, AC4.2, AC4.6, AC4.7, AC5.1 changed) (Tanay Deo)
- 23 October 2026 — Setup lists each module's real Edge keys as the key picker shows them; ticking a key asks for a Display Name used on the Reports page. US2 and US3 rewritten; old Q2 (segmentation "Detected classes") dropped as segmentation offers only its keys; new Q2 (running totals) and Q3 (values with no key) (Tanay Deo)
- 23 October 2026 — Duplicate pass: AC8.4, AC11.1 and AC11.3 no longer restate AC1.1, AC4.3 and AC4.5 (Tanay Deo)
- 23 October 2026 — Moved to the current template: open questions now sit in Discussion points at the top; added Q2 (segmentation "Detected classes"); Word copy dropped, the PRD repo is the only published copy (Tanay Deo)
- 23 October 2026 — Prototype rebuilt as one interactive workflow with a step navigator; stories link to their step (Tanay Deo)
- 20 October 2026 — Draft started (Tanay Deo)
- 20 October 2026 — Prototype live on GitHub Pages; sent to the PM for review (Tanay Deo)
- 20 October 2026 — First full draft: 11 user stories after two decision rounds (setup in Reports, all module types, Update permission, one Excel column, past cycles shown, output values only, Tracker out, latest attempt) (Tanay Deo)

## Checked, not affected
- vision-access, vision-auto-annotation, vision-captures-cameras, vision-datasets-annotations, vision-instance-creation, vision-model-evaluation, vision-model-training, vision-projects, vision-recipe-tree, vision-training-sets-recommender, vision-variants: Edge-only feature; every name it needs is already on the box after upload, and the instance package does not change.
- mod-input-master-image, mod-search-area, mod-masking: no values of their own; their names are only used as labels.
- edge-plc-read, edge-plc-write, edge-plc-setup, edge-internal-actions, edge-dashboard-builder, edge-live-dashboard: rules, keys and dashboards are untouched.
- edge-camera-capture, edge-model-loading-variant-change, edge-storage-image-saving: capture, model loading and saved images are untouched.
- engine-runtime-packaging, edge-installer, edge-encryption-keys, edge-networking, edge-system-time: no new libraries, steps, keys, ports or time logic; the Reports app keeps its ports.
