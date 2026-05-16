Sentence Comprehension with Digit Memory – Dual‑Task Experiment
Overview
This experiment investigates how verbal working memory load affects the resolution of relative clause attachment ambiguities in English. Participants read sentences like:

The driver of the players who was/were talking to an old woman.

and decide whether the relative clause (who was/were talking) refers to the first noun (driver) or the second noun (players). A secondary 4‑digit recall task is used to load working memory in half of the trials.

The experiment is implemented in jsPsych (v7.3.4) and runs entirely in a web browser. No server‑side installation is required.

Features
Counterbalanced design

Block order (baseline‑first / dual‑first)

Item‑set assignment (which items go to each block)

HA/LA list (High‑Attachment vs. Low‑Attachment version for each of 32 experimental pairs)

Key mapping (C = NP1 / M = NP2, or swapped)

Dual‑task manipulation

Baseline block: sentence → question only

Dual‑task block: 4‑digit sequence → sentence → question → digit recall

Data logging

Comprehension response (NP1/NP2), reaction time, accuracy

Digit recall accuracy and entered digits

All data sent to a Google Sheets endpoint (Apps Script)

Catch trials (3 total) to identify inattentive participants.

Conditional demographics

Standard fields (age, gender, education, handedness, native language, other languages)

“Years of formal English study” and “Self‑rated proficiency” appear only when the participant is not a native English speaker, and are required for non‑native speakers.

Mobile device detection – blocks phones/tablets without physical keyboards; warns touchscreen laptops.

Progress counter – shows number of completed responses out of 92.

Self‑contained HTML – no external files; all stimuli are embedded.

File Structure
text
experiment/
├── index.html          # The complete experiment (copy this to your web server or run locally)
└── README.md           # This file
No other files are required.

Setup Instructions
1. Configure the Google Sheets endpoint
The script sends data to a Google Apps Script URL. You must replace the existing URL with your own.

Create a Google Sheet and an Apps Script (bound to the sheet) that accepts POST requests and appends rows.
A sample Apps Script is provided at the end of this README.

Deploy the script as a web app (execute as “Me”, access “Anyone”).

Copy the deployment URL (looks like https://script.google.com/macros/s/…/exec).

Open index.html and replace the value of GOOGLE_SHEETS_URL with your URL.

javascript
const GOOGLE_SHEETS_URL = "https://script.google.com/macros/s/YOUR_SCRIPT_ID/exec";
2. (Optional) Change researcher email
The consent form includes i.zeddari@um5r.ac.ma. Replace it with your own email address in the consent card stimulus.

3. Host the file
Local testing: Save index.html and open it in Chrome/Firefox/Edge.
⚠️ Some browsers may block local file access to external scripts; use a local web server (e.g., python -m http.server 8000) for full functionality.

Online deployment: Upload index.html to any static web hosting (GitHub Pages, Netlify, etc.).

Running the Experiment
Participant opens the index.html URL in a desktop browser (requires physical keyboard).

Reads the consent form and presses C to agree.

Fills in the demographics (native speakers skip the English proficiency fields).

Reads instructions, then completes two blocks (baseline and dual‑task) in counterbalanced order.

After finishing, a completion screen appears; data are automatically logged to Google Sheets.

Data Collected (per participant)
Field	Description
participant	Self‑chosen ID
age, gender, education, handedness	Demographics
native_language, other_languages	Language background
native_english	“yes” or “no”
years_english	Number of years of formal English study (or “N/A” for natives)
proficiency	Self‑rated proficiency (1‑7, or “N/A” for natives)
list_type	A or B (HA/LA list assignment)
key_mapping	original or swapped
block_order	baseline_first or dual_first
set_assignment	normal or swapped
block	baseline_main, dual_main, practice_baseline, practice_dual
trial_num	Sequential number within block
sentence, question, correct_answer, response, accuracy	Trial data
rt_from_question	Reaction time (ms)
digit_sequence	The 4‑digit sequence shown (dual‑task only)
recall_correct, recalled_digits	Digit recall accuracy and entered digits (separate row, is_recall_row: true)
is_dual_task	true/false
item_type	pair, ambiguous, filler, catch
Sample Google Apps Script (for data logging)
Create this script in your Google Sheet (Extensions → Apps Script), then deploy as a web app.

javascript
function doPost(e) {
  try {
    const data = JSON.parse(e.postData.contents);
    const sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
    
    // Add headers if sheet is empty
    if (sheet.getLastRow() === 0) {
      const headers = Object.keys(data);
      sheet.appendRow(headers);
    }
    
    const headers = sheet.getRange(1, 1, 1, sheet.getLastColumn()).getValues()[0];
    const rowData = headers.map(header => data[header] !== undefined ? data[header] : "");
    sheet.appendRow(rowData);
    
    return ContentService.createTextOutput(JSON.stringify({ result: "success" }))
      .setMimeType(ContentService.MimeType.JSON);
  } catch (error) {
    return ContentService.createTextOutput(JSON.stringify({ error: error.toString() }))
      .setMimeType(ContentService.MimeType.JSON);
  }
}
Customisation
Change digit length – modify digitSequences and the pattern attribute in recallTrial (from [0-9]{4} to [0-9]{5} for 5 digits).

Adjust sentence duration – change trial_duration: 5000 in the sent trial.

Modify catch trials – edit the catchTrialsPool array.

Add or remove experimental items – add/remove entries in rawPairs, ambiguousRaw, or fillerRaw.

Troubleshooting
Problem	Solution
Page remains “Loading experiment…”	Check browser console (F12) for errors. Ensure you have an active internet connection to load jsPsych from CDN.
“Continue” button does nothing (demographics)	Make sure you selected either Yes or No for the native speaker question and filled all required fields. Non‑native speakers must enter years of study and proficiency.
Data not appearing in Google Sheet	Verify your Apps Script is deployed as a web app and the URL is correctly placed in GOOGLE_SHEETS_URL. Test with a manual POST (e.g., using curl or Postman).
Mobile device warning appears on a laptop with touchscreen	This is normal; the warning reminds you to use the physical keyboard. Press SPACE to dismiss.
License
This script is provided for academic research purposes. You may modify and use it freely. Please credit the author (Ikbal Zeddari) in any publications.

Contact
For questions or support, contact: i.zeddari@um5r.ac.ma

Last updated: May 2026
