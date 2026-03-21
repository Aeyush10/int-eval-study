# Survey Form — Setup Guide

A lightweight survey system that pulls questions from Google Sheets,
shows a random subset to each participant, and writes responses back.

---

## Files

| File              | What it is                                        |
|-------------------|---------------------------------------------------|
| `survey.html`     | The frontend — host this on any static host       |
| `apps-script.js`  | Backend code — paste into Google Apps Script       |

---

## Step-by-step setup

### 1. Create your Google Sheet

Create a new Google Sheet with **two tabs** (rename the default sheet):

**Tab: "Questions"** (columns A–C, row 1 is the header)

| QuestionID | Text                          | Options                              |
|------------|-------------------------------|--------------------------------------|
| Q1         | What is your favourite color? | Red,Blue,Green,Yellow                |
| Q2         | How often do you exercise?    | Daily,Weekly,Rarely,Never            |
| Q3         | Preferred work style?         | Remote,Hybrid,Office,No preference   |
| ...        | ...                           | ...                                  |

- **Options** = exactly 4 comma-separated values per row (no spaces after commas, or spaces are fine — the code trims them)

**Tab: "Responses"** (columns A–D, row 1 is the header)

| Timestamp | ParticipantID | QuestionID | Response |

Leave it empty — the script will append rows here.

---

### 2. Add the Apps Script

1. In your Google Sheet → **Extensions → Apps Script**
2. Delete any existing code in `Code.gs`
3. Paste the entire contents of `apps-script.js`
4. Click **Save** (💾)

---

### 3. Deploy the Apps Script

1. Click **Deploy → New deployment**
2. Click the gear icon ⚙️ → select **Web app**
3. Set:
   - **Description**: "Survey backend" (or anything)
   - **Execute as**: **Me**
   - **Who has access**: **Anyone**
4. Click **Deploy**
5. **Authorize** when prompted (click through the "unsafe" warning — it's your own script)
6. **Copy the Web app URL** — it looks like:
   `https://script.google.com/macros/s/AKfycbx.../exec`

---

### 4. Configure the frontend

Open `survey.html` in a text editor and find these two lines near the bottom:

```javascript
const APPS_SCRIPT_URL = "";          // paste your web app URL here
const QUESTIONS_TO_SHOW = 5;         // how many random questions to show
```

- Paste your Web app URL between the quotes
- Change `5` to however many questions you want each participant to see

---

### 5. Host the frontend

Upload `survey.html` to any free static host:

- **GitHub Pages**: push to a repo, enable Pages in settings
- **Netlify**: drag-and-drop the file at app.netlify.com/drop
- **Vercel**: import a repo or use the CLI

Or just open the HTML file locally in a browser to test.

---

## How it works

```
Participant opens site
        │
        ▼
  GET → Apps Script → reads "Questions" sheet → returns JSON
        │
        ▼
  JavaScript picks random subset, generates participant ID
        │
        ▼
  Participant answers & clicks Submit
        │
        ▼
  POST → Apps Script → appends rows to "Responses" sheet
        │
        ▼
  Thank-you screen shown with participant ID
```

Each question-response pair gets its own row:

| Timestamp           | ParticipantID | QuestionID | Response |
|---------------------|---------------|------------|----------|
| 2026-03-21 10:15:00 | P-8A3F2C      | Q7         | Blue     |
| 2026-03-21 10:15:00 | P-8A3F2C      | Q3         | Remote   |
| 2026-03-21 10:15:00 | P-8A3F2C      | Q12        | Daily    |

---

## Customization

- **Change the title/description**: edit the `<h1>` and `<p>` in the header section of `survey.html`
- **Change colors**: edit the CSS variables in `:root { ... }` at the top
- **Add more than 4 options**: the code handles any number — just add more comma-separated values in the Options column
- **Change number of random questions**: update `QUESTIONS_TO_SHOW`

---

## Troubleshooting

| Problem                        | Fix                                                      |
|--------------------------------|----------------------------------------------------------|
| "Setup required" banner shows  | You forgot to paste the Apps Script URL                  |
| CORS error in console          | Make sure the Apps Script is deployed as "Anyone" access |
| No questions load              | Check your "Questions" tab name is exactly "Questions"   |
| Responses not appearing        | Check your "Responses" tab name is exactly "Responses"   |
| Old version still showing      | After editing Apps Script, do Deploy → Manage → New version |


## Questions sheet URL
https://docs.google.com/spreadsheets/d/e/2PACX-1vSxkG69fQa9h3e2if5SC5xBtAjbwVBr-8SUoYRBsBbadBBV6UD63MLwaT-n07zzH6lLVyiSM_cgAh78/pub?gid=0&single=true&output=csv


## Web App URL
https://script.google.com/macros/s/AKfycbwKxKWJrv5nyIj3nG5rJDyu5c40DAPvQriBHiZJyfyaN2f4svUrU9nEU50w69l-urMdOg/exec