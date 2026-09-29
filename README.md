# Grading Console Prototype

A browser-based prototype for reviewing student marks, configuring grade bands, and exporting course grades. **This is not an official BITS Pilani Digital grading tool and must not be used for actual academic grading.**

## 🐛 Bug Fix Log

This section documents issues reproduced in the supplied prototype, their root causes, and the fixes applied.

| # | Bug / Issue Identified | How You Reproduced It | Root Cause | Fix Implemented | How You Tested the Fix |
|---|------------------------|-----------------------|------------|-----------------|------------------------|
| 1 | `.xlsx` workbooks could not be selected. | Checked the file picker and attempted to upload a generated `.xlsx` workbook. | The input accepted only `.xls`, and the reader used binary-string parsing. | Accept `.xlsx` and `.xls`; read workbooks as array buffers. | Uploaded generated `.xlsx` workbooks in the browser and confirmed records loaded. |
| 2 | Course options were duplicated or left over after another upload. | Uploaded a workbook with multiple Course A rows, followed by a workbook containing Course B. | A course option was appended for every row; old options were not cleared. | Deduplicate and sort course names, then replace the list after a valid upload. | Course A appeared once; the second upload showed only Course B. |
| 3 | Invalid marks and malformed rows could silently be graded. | Uploaded a workbook containing a fractional mark (71.5). | Worksheet rows were accepted without checking the required columns or values. | Validate the exact three headers, required fields, integer marks from 0–100, and duplicate student IDs before replacing active data. | The fractional mark was rejected with a row-specific message; the last valid workbook remained available. |
| 4 | Minimum and maximum values appeared under the wrong labels. | Uploaded marks including 0 and 100 and compared the displayed statistics. | The Min and Max element IDs were reversed in the markup. | Match each label to its metric and show the course student count. | The browser showed minimum 0, maximum 100, and the expected student count. |
| 5 | Empty data produced invalid analytics and could permit an empty export. | Exercised the empty state and inspected the displayed statistics and export control. | Statistics divided by an empty count, and export availability did not depend on roster size. | Display placeholders for unavailable statistics and disable export unless the selected course has students. | Empty state showed dashes and count 0; export was disabled with an explanatory message. |
| 6 | Invalid grade boundaries could leave marks ungraded while allowing export. | Changed Grade A's maximum from 100 to 99. | Validation checked continuity between bands but not that the first and last bands covered the full 0–100 scale. | Require Grade A to include 100, Grade E to include 0, and adjacent bands to remain continuous. | Setting the maximum to 99 showed a validation error and disabled export; restoring 100 cleared it. |
| 7 | CSV values were not escaped and formula-like text was unsafe in spreadsheet software. | Exported with a formula-like instructor name (`=Instructor`) and inspected the generated CSV. | Values were concatenated directly into CSV rows. | Quote and escape cells, prefix formula-like text, add a UTF-8 BOM, and use a course-specific filename. | Captured output safely represented the instructor and included the expected headers and student grade rows. |
| 8 | The timer included time before grading started and initially waited for an interval tick. | Opened the app without selecting a course and observed the timer. | Timing began at page load, and the display was not updated immediately. | Start timing when a course is selected and render the initial timer value immediately. | Confirmed the page begins at `00:00`; the timer starts with grading. |

## 🚀 Enhancements Implemented

1. **Safer workbook workflow**: accepts `.xlsx` and `.xls`, validates the required schema and mark values, prevents duplicate IDs within a course, and preserves the last valid upload when a new workbook is rejected.
2. **Clearer course analytics**: correct minimum/maximum labels, average, median, student count, grade totals, and a refreshed histogram with an explicit empty state.
3. **More reliable grading and export**: grade bands must cover the complete 0–100 scale without gaps or overlaps; empty rosters cannot be exported; CSV cells are escaped and protected against formula injection.
4. **Instructor-focused usability**: responsive layout, labeled and keyboard-visible controls, live status/error announcements, a grading-session timer, and a single-confirmation-free range reset.

## 📊 Input Workbook

Upload an Excel workbook with exactly these columns:

- `BITS ID`
- `Course`
- `Total Marks`

Marks must be whole numbers from 0 to 100. Keep BITS IDs formatted as text in Excel if they contain leading zeroes. Students who should receive an NC grade should not be included.

## ▶️ Run Locally

Open `BITS_Digital_CodeForge_Challenge.html` in a modern browser. The app uses SheetJS from a CDN to read Excel files, so an internet connection is required for the library to load.

## 🌐 Deployment

- **GitHub Repository:** Not published yet.
- **Live Application URL:** Not deployed yet.

The app is a static HTML page and can be hosted on GitHub Pages or another static hosting service. Publishing still requires an authenticated GitHub or hosting account.

## 📋 Summary

- Fixed and documented eight reproducible issues.
- Added workbook validation, clearer analytics, safer grading/export, accessibility improvements, and a responsive layout.
- Tested the import-to-export workflow with generated `.xlsx` workbooks, including invalid data and empty-state checks.
- Public deployment remains outstanding; no repository or live URL has been created.
