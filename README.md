Language Processing under Cognitive Load: Relative Clause Attachment
An online psycholinguistic experiment built using jsPsych (v7.3.4). This study implements a dual‑task paradigm crossing structural ambiguity resolution with a working memory load component to investigate sentence processing differences between English as a Foreign Language (EFL) learners and Native English speakers.

 Experimental Design Matrix
The study implements a 2 × 2 × 2 mixed factorial design:

Factor	Type	Levels
Language Group	Between‑subjects (quasi‑independent)	Native Speakers vs. EFL Learners
Memory Load Condition	Within‑subjects	Baseline (No Load) vs. Dual‑Task (5‑digit retention)
Item Disambiguation Profile	Within‑subjects / Items	Pair: Disambiguated target items (High Attachment / Low Attachment via verb agreement); Ambiguous: Globally ambiguous baseline controls
Note on quasi‑experimental status: Language group is a pre‑existing participant characteristic and cannot be randomly assigned. Causal claims about “being an EFL learner” are not made; the native group serves as a control to benchmark task performance and isolate the effect of working memory load within the EFL population.

 Stimulus Profile Allocation
The experiment draws from a master pool of 84 items, automatically counterbalanced across participants:

Category	Count	Allocation
Experimental Target Pairs (disambiguated)	32 items	16 per set (A / B)
Ambiguous Controls	32 items	16 per set (A / B)
Fillers	20 items	10 per set (A / B)
Attention Checkers (Catch Trials)	3 items	Explicit attention validation
Total trials per block: 43–44 experimental trials + 4 practice trials.

Transparency note on ambiguous items: Ambiguous items have no objectively correct answer. In the power simulation, all 64 experimental items (pairs + ambiguous) were treated as having a binary outcome with balanced attachment bias. This simplification is conservative (adds noise) and does not inflate power estimates. The true power for detecting the critical interaction is expected to be at least as high as reported.

 Script Architecture & Task Flow
The task structure dynamically shifts across experimental phases to isolate memory maintenance mechanics:

text
                  ┌──────────────────────────────┐
                  │      URL Query Parameter     │
                  │   (?group=EFL or =native)    │
                  └──────────────┬───────────────┘
                                 │
                                 ▼
                  ┌──────────────────────────────┐
                  │     Consent & Screening      │
                  │   (Blocks Mobile Access)     │
                  └──────────────┬───────────────┘
                                 │
                                 ▼
                  ┌──────────────────────────────┐
                  │  Dynamic Demographics Form  │
                  │  (Adapts based on Group)     │
                  └──────────────┬───────────────┘
                                 │
         ┌───────────────────────┴───────────────────────┐
         ▼                                               ▼
┌─────────────────────────────────┐             ┌─────────────────────────────────┐
│ BLOCK ORDER A                   │             │ BLOCK ORDER B                   │
│                                 │             │                                 │
│ 1. Baseline Block               │             │ 1. Dual-Task Block              │
│    - 4 Practice Trials (w/ FB)  │             │    - 4 Practice Trials (w/ FB)  │
│    - 43 Experimental Trials     │             │    - 44 Experimental Trials     │
│                                 │             │                                 │
│ 2. Dual-Task Block              │             │ 2. Baseline Block               │
│    - 4 Practice Trials (w/ FB)  │             │    - 4 Practice Trials (w/ FB)  │
│    - 44 Experimental Trials     │             │    - 43 Experimental Trials     │
└────────────────┬────────────────┘             └────────────────┬────────────────┘
                 │                                               │
                 └───────────────────────┬───────────────────────┘
                                         │
                                         ▼
                  ┌──────────────────────────────┐
                  │     Experiment Complete      │
                  │  (Displays autoParticipantID)│
                  └──────────────────────────────┘
 Trial Phase Sequences
Baseline Block Trial Structure
Fixation Cross (+) – 500 ms

Target Sentence Reading – Self‑paced (keypress records reading time)

Comprehension Question – Keypress (C / M) records choice and response time

Visual Feedback – 1000 ms (practice trials only)

Dual‑Task Block Trial Structure
Fixation Cross (+) – 500 ms

Digit Sequence Presentation – 2000 ms (5 pseudo‑randomised digits)

Target Sentence Reading – Self‑paced

Comprehension Question – Keypress (C / M)

Digit Memory Recall – HTML text form, 4‑digit sequence validation

Visual Feedback – 1500 ms (practice trials only; feedback on both sentence and digit recall)

 Technical Features
Dynamic Counterbalancing Engine:

Block order (baseline‑first vs. dual‑first)

Set assignment (set A / set B)

Response mapping (C = NP1 / M = NP2, or swapped) – randomised per participant.

Asynchronous Data Pipeline:
Each trial response is formatted into a long‑format payload and uploaded via fetch() to a Google Sheets backend, preventing data loss if a participant drops out.

Mobile Exclusion:
The script detects touchscreen devices and blocks execution, requiring a physical keyboard (laptop/desktop) to maintain data precision.

Deployment Instructions
1. Configure the Spreadsheet Data Backend
Open a new Google Sheet.

Navigate to Extensions → Apps Script and deploy a Web App script that appends incoming JSON data to the sheet.

Authorise the script as “Me” and set access to “Anyone”.

Copy the generated Web App URL.

In index.html, locate the scriptURL variable and update it:

javascript
const scriptURL = 'https://script.google.com/macros/s/AKfycbx-KkNp_g4rJ5ikUH0YSlGUH2Q43ULOpWobQ5ZR_FCNlY8H84nRRW8o1EG9IezXOpo/exec';
2. Runtime Execution Configuration
The experiment uses URL parameters to assign groups. Use the following links:

Group	URL
EFL group: https://izeddari.github.io/Dual-Task-RC-Experiment/?group=EFL 
Native group: https://izeddari.github.io/Dual-Task-RC-Experiment/?group=native
Collected Metrics & Variables
The data pipeline sends one row per trial (plus a separate row for digit recall in dual‑task blocks). Columns include:

Column	Type	Description
participant_id	String	Unique non‑identifying ID (user‑provided)
auto_participant_id	String	Auto‑generated code (e.g., auto_<timestamp>_<random>)
group	String	EFL or native
block_order	String	baseline_first / dual_first
stimulus_set	String	set_a / set_b
key_mapping	String	original (C=NP1,M=NP2) / swapped
age, gender, education, handedness, native_language	Various	Demographics
years_english, proficiency	Integer / String	Only for EFL group (self‑rated)
trial_type	String	baseline_main, dual_main, practice, etc.
item_id	String	Item identifier
item_type	String	pair, ambiguous, filler, catch
condition	String	Baseline / Dual
reading_time	Integer	Sentence reading RT (ms)
question_rt	Integer	Question response RT (ms)
accuracy	Integer	1 = correct, 0 = incorrect (null for ambiguous items)
digit_load	String	4‑digit sequence presented
digit_response	String	Participant’s recalled digits
digit_correct	Integer	1 = recall correct, 0 = error
📄 Associated Files
Power analysis script (power_sensitivity_RC_attachment_64items.R) – Monte Carlo power simulation using lme4 and simr.

Power analysis report – Describes parameters, sensitivity curve, and interpretation.

 Author & Contact
Principal Investigator: Ikbal Zeddari
Institution: Mohammed V University in Rabat (UM5R)
Email: i.zeddari@um5r.ac.ma
