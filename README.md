# CampusAssist

A concept prototype of an AI-assisted student help desk with instant practice-quiz feedback, built for the ZCAS (Zambia Centre for Accountancy Studies) context.

Made as the practical project for **CIT1142 Emerging Trends in ICT** (trend: *Artificial Intelligence and Automation*).

- **Author:** Manoah Mwansa Chama (202500902), BSc Software Engineering
- **Lecturer:** Alex Ng'uni

> **Important:** CampusAssist is **not a generative AI model**. It is a small, classical language-processing engine written in JavaScript. It cannot write new answers. It only selects approved answers and shows where they came from.

## What it does

The page has three tabs:

| Tab | What it does |
|---|---|
| **Help desk** | Answers common questions (applications, location, contacts, fees, payments, programmes, degrees, legal status) from a small approved knowledge base. Each answer shows its source and a confidence score. |
| **Quiz feedback** | Three practice questions with instant hints and feedback. Graded assessment would still be marked by lecturers. |
| **Lecturer test** | Runs three test sets (38 questions) and shows the expected and actual result for every question. |

If a question is sensitive, or the system is not confident, it does **not** guess. It creates a ticket for a human staff member. Tickets can be downloaded as `tickets.csv`.

## How to run it

No installation, no server and no internet connection are needed.

1. Download `CampusAssist_JS.html` from this repository (open the file, then use the download button).
2. Double-click the file. It opens in Chrome, Edge, Firefox or Safari, on a computer or a phone.

### Optional: open it as a web page with GitHub Pages

1. In this repository go to **Settings → Pages**.
2. Under **Build and deployment**, choose **Deploy from a branch**, select `main` and the `/ (root)` folder, then save.
3. After a minute or two the page should be available at
   `https://spaceski.github.io/CampusAssist/CampusAssist_JS.html`
   (GitHub shows the exact link on the Pages screen).

## How the "AI" works

Everything runs in the browser, inside the one HTML file.

1. **Text pipeline.** The question is lowercased, split into words, common words are removed and the rest are reduced to a simple stem.
2. **TF-IDF retrieval.** The question and each stored example phrase or answer are turned into TF-IDF vectors. Cosine similarity picks the closest approved answer.
3. **Sensitive-topic screening.** A Naive Bayes classifier, trained on example phrases, flags topics such as appeals, complaints and wellbeing. A short word list catches urgent wellbeing messages and sends them straight to a human.
4. **Decision rules.**
   - Similarity below `0.30`: not found, so a human ticket is created.
   - Sensitive topic: a human ticket is created.
   - Two answers within 80% of each other: both answers are shown.
   - Otherwise: the best answer is shown with its source and confidence.

## Knowledge base

Eight entries taken from public pages on the ZCAS website (zcas.ac.zm): how to apply, address, contact details, fees, payment, professional programmes, degrees and legal status. Fee amounts are not stored. The answer points to the official fees page instead.

**ZCAS staff must check these entries before any real use.**

## Test results

Expected outcomes were written before each run. The first keyword-matching version of the prototype was used as a baseline.

| Test set | Questions | Keyword baseline | TF-IDF engine |
|---|---|---|---|
| Set 1: first test (used while developing) | 10 | 9 of 10 | 10 of 10 |
| Set 2: reworded questions (used while developing) | 14 | 10 of 14 | 11 of 14 |
| Set 3: fresh questions (written after tuning) | 14 | 11 of 14 | 11 of 14 (79%) |

Set 3 is the fair test. On unseen questions the engine did **not** beat the simple keyword version. Four of its six misses on Sets 2 and 3 showed two answers with the right one included. Two were confident wrong answers to out-of-scope questions (a weather question mentioning Lusaka returned the address, and "I lost my student ID card" returned the application answer). Run the **Lecturer test** tab to see every result.

## Limitations

- Small knowledge base (8 entries) and small test sets (38 questions in total).
- Tests were written and run by one person, and used public information only.
- No students were surveyed, so the size of the real waiting problem at ZCAS is not measured.
- Not connected to any ZCAS system, so it cannot give account-specific answers.
- Tickets are kept only in the browser session and are not sent to anyone.

## Privacy

The page makes no network requests. Questions typed into it stay in your browser and are not sent anywhere. A real deployment would need consent, a privacy notice and hosting that follows Zambia's Data Protection Act No. 3 of 2021.

## Changing what it knows

Open `CampusAssist_JS.html` in a text editor and find `const DATA`. Edit the entries under `kb` (each has an `id`, `source`, `answer` and a list of example phrases). The phrases under `sensitive` and `normal` train the sensitive-topic classifier. Save the file and reload it in the browser.

## Files

| File | Purpose |
|---|---|
| `CampusAssist_JS.html` | The whole prototype (page, styling, engine and data in one file) |
| `README.md` | This file |

## Credits and sources

Knowledge-base content comes from the public ZCAS website. The design follows the evidence discussed in the accompanying report, including the Georgia State University AI-assistant experiment (Page and Gehlbach, *AERA Open*, 2017) and the feedback research of Hattie and Timperley (2007).
