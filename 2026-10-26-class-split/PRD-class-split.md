# PRD: Class Instances Split on the Training Results page

**Prototype:** [Class Instances Split Prototype](https://frinks-ai.github.io/frinks-PRDs/2026-10-26-class-split/prototype/)
**Product:** Frinks Vision
**Status:** Approved
**Date:** 26 October 2026
**Owner:** Akshat Sharma
**Related:** FP-1645, FP-1646 (original Training Results graphs), FP-2144 (OCR training results, To Do)

---

## Why are we making this?

One AI model trains several similar classes (ODC, Segmentation, IC classes, or OCR characters), and these classes appear across captures in different combinations. Some classes occur many times in the dataset while others barely occur at all, so the model becomes biased towards the classes with more data. Today the Training Results page only shows the total number of images per split (Training / Testing / Validation), so users cannot see this imbalance before deploying a model.

This ticket adds a class-wise split of the Training, Testing and Validation data on the Training Results page.

## Executionals

### E1 - Save class counts when training starts

**User story:** As an ML engineer, I want the per-class counts of each data split saved with the model so that the Training Results page always shows the data that model was actually trained on.

**Acceptance criteria:**

- **AC1.1** Given a model training starts, when the dataset is split into Training, Testing and Validation data, then the image count of each split and the per-class count of each split are saved with that model version.
- **AC1.2** Given an ODC or Segmentation model, when the counts are saved, then each class count is the number of annotated instances (boxes or masks) of that class in the split.
- **AC1.3** Given an IC model, when the counts are saved, then each class count is the number of images labelled with that class in the split.
- **AC1.4** Given an OCR model, when the counts are saved, then each character count is the number of annotated occurrences of that character in the split.
- **AC1.5** Given a trained model, when its dataset's annotations are edited or images are added later, then the saved counts for that model do not change.
- **AC1.6** Given a class that has no annotations in a split, when the counts are saved, then that class is saved with a count of 0 for that split (not omitted).

### E2 - Class Instances Split section replaces the top split strip

**User story:** As an ML engineer, I want the data split shown as its own section on the Training Results page so that I can read it alongside the validation and training results.

**Acceptance criteria:**

- **AC2.1** Given the Training Results page of a trained model, when it loads, then the line "Training Data: N images / Testing Data: N images / Validation Data: N images" is no longer shown above Validation Results.
- **AC2.2** Given the page loads, then a new section is shown below the Confidence Threshold card (end of Validation Results) and above the Training Results heading.
- **AC2.3** Given an ODC, Segmentation or IC model, then the section heading reads "Class Instances Split"; given an OCR model, then it reads "Character Instances Split".
- **AC2.4** Given the section, then a one-line description under the heading states what the counts are and that a class with far fewer examples than the others can bias the model (copy as in the prototype).
- **AC2.5** Given the page, then the Validation Results and Training Results sections (image comparison, confidence threshold Save/Reset, confusion matrix, loss curve) behave exactly as before.

### E3 - Class instances split table

**User story:** As an ML engineer, I want to see the per-class counts for each split in one table so that I can spot classes with too little data before deploying the model.

**Acceptance criteria:**

- **AC3.1** Given the section, then a table shows the columns: Class (Character for OCR), Training Data, Testing Data, Validation Data.
- **AC3.2** Given the table, then each split column header shows the split name with that split's image count below it, e.g. "Training Data / 114 images".
- **AC3.3** Given the table, then each row is one class (or character) with its count for each split as a plain number - no bars, percentages or icons.
- **AC3.4** Given an ODC, Segmentation or IC model, then rows are sorted by Training count, highest first, with ties in alphabetical order; given an OCR model, then rows are ordered 0-9 then A-Z.
- **AC3.5** Given a browser window 1280px wide or wider, then the table fits the page width without horizontal scrolling, and class names up to 40 characters wrap inside the Class column instead of widening the table.
- **AC3.6** Given an OCR model, then the table lists every character in the model's character set, including characters with 0 in all splits.

### E4 - Zero counts highlighted

**User story:** As an ML engineer, I want missing data called out so that I notice a class that was never trained, tested or validated.

**Acceptance criteria:**

- **AC4.1** Given a count of 0 in any cell, then that number is shown in the platform's error red, while all other counts use the default text colour.
- **AC4.2** Given a model with no zero counts, then no cell is shown in red.

### E5 - Show all / Show less

**User story:** As an ML engineer, I want the table to be short by default so that it does not push the Training Results section far down the page.

**Acceptance criteria:**

- **AC5.1** Given more than 3 rows, when the page loads, then only the first 3 rows (in the E3 order) are shown, with blue clickable text below the table reading "Show all N classes" ("Show all N characters" for OCR), where N is the total number of rows.
- **AC5.2** Given the collapsed table, when the user clicks "Show all N ...", then the table expands in place to show all rows, with no page navigation or modal, and the text changes to "Show less".
- **AC5.3** Given the expanded table, when the user clicks "Show less", then the table returns to 3 rows.
- **AC5.4** Given 3 or fewer rows, then all rows are shown and no "Show all" text is shown.
- **AC5.5** Given the user reloads the page or opens another model, then the table starts collapsed.

## Out of scope

- Behaviour for models trained before this release (to be added later to this ticket as E6)
- Imbalance warnings, flags or recommendations
- Percentages, bars or charts in the table
- Pixel-area share for segmentation (counts are mask instances only)
- A row for background / negative images
- Changes to the confusion matrix, loss curve or validation image comparison
- Editing or adding data from this page

## Migration

**Needed:** Y

## Release note

**Operator-visible:** Y

**Note:** The Training Results page now shows how many examples of each class (or each character, for OCR models) are in the training, testing and validation data, so you can spot classes with too little data before deploying a model.

**Upgrade action:** None

## Screens to capture

- **[S1]** E2, E3, E5 | **Where:** Vision > AI Models > (an ODC model) > Training Results | **State:** ODC model with 4 or more classes, one class with 0 in Validation Data, table collapsed | **Highlight:** Class Instances Split section | **Caption:** See how much data each class has in every split

## Dev test cases

To be added by the developer. Map each case to an executional ID (E1, E2...).

## Ticket metadata

- **Affected Product:** FrinksVision
- **Component / Sub-component:** Models & Modules / Model - Training
- **Type (layer):** Frontend + Backend (training pipeline)
- **Fix version:** TBD - to be set once the 26.x release version exists in Jira
- **Migration needed:** Y
- **Related:** FP-1645, FP-1646 (original Training Results graphs), FP-2144 (OCR training results, To Do)

## Changelog

- 27 Oct 2026 — Renamed "Class Split" to "Class Instances Split": PRD title, E2 title, E3 title, AC2.3 heading text, S1 highlight, prototype heading. No behaviour change (Tanay)
- 27 Oct 2026 — AC2.3 OCR heading "Character Split" → "Character Instances Split"; prototype OCR heading updated (Tanay)
- 28 Oct 2026 — Status Draft → Approved; no executional or AC changed (Tanay)
