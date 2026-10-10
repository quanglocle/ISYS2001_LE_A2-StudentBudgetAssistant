# Student Budget Assistant

A small finance assistant for international university students in Perth. Upload a month of bank transactions and it shows where your money went, checks your spending against budgets you set, plans a savings goal, and lets you ask **Penny**, a Gemini-powered budget coach, questions about your own numbers.

Built for ISYS2001 Introduction to Business Programming, Assessment 2.

## The problem

Students on a casual-job income lose track of small, frequent purchases (food delivery, subscriptions). Bank apps list transactions but don't say "you're 40% over on eating out" or "save $90 a week to afford your flight home". This assistant turns the student's own transaction list into those answers.

## Features

| Tab | What it does |
|---|---|
| **1. Upload** | Reads your CSV, cleans it (dollar signs, blank categories, bad rows) and shows income, spending, what's left over and spending by category |
| **2. Budget checker** | *Custom tool.* Type limits like `Groceries=300, Eating Out=120`; each category is marked 🟢 Under, 🟠 Close to limit (≥ 80%) or 🔴 Over |
| **3. Savings goal** | *Custom tool.* Enter a goal, what you've saved and a deadline; it tells you the weekly amount needed and whether your current surplus (pre-filled from your data) is enough |
| **4. Ask Penny** | Chat with a Gemini model that is given your real figures and a budget-coach persona; it declines investment/tax advice and off-topic questions |

## How to run it

1. Open `finance_assistant.ipynb` in [Google Colab](https://colab.research.google.com/) (File → Upload notebook, or open it from GitHub).
2. Get a free Gemini API key from [Google AI Studio](https://aistudio.google.com/apikey).
3. In Colab, click the  **Secrets** icon on the left, add a secret named `GEMINI_API_KEY` with your key, and switch on *Notebook access*. The key is never written in the notebook or this repository.
4. **Runtime → Run all.** The app appears under the `app.launch()` cell; the test results print at the bottom.

Everything except the chat tab works without an API key. The notebook creates its own copy of the sample CSV files, so nothing needs to be downloaded or uploaded first.

## Your own data

Any CSV with these columns works:

```
date,description,category,amount
2026-09-01,Casual job pay,Income,520.00
2026-09-02,Coles,Groceries,64.35
```

Use the category `Income` for money coming in. Other categories are counted as spending; a negative amount is treated as a refund.

## Sample input and output

Input: `data/sample_transactions.csv` (one month, 35 transactions) with budgets `Groceries=300, Eating Out=120, Subscriptions=20, Transport=100`.

Summary tab:

```
Income:            $2,510.00
Spending:          $2,161.92
Left over:           $348.08
Biggest category:  Rent
```

Budget checker:

| Category | Spent | Budget | Remaining | % used | Status |
|---|---|---|---|---|---|
| Groceries | $352.15 | $300.00 | -$52.15 | 117.4% | 🔴 Over |
| Eating Out | $169.85 | $120.00 | -$49.85 | 141.5% | 🔴 Over |
| Subscriptions | $50.97 | $20.00 | -$30.97 | 254.8% | 🔴 Over |
| Transport | $90.00 | $100.00 | $10.00 | 90.0% | 🟠 Close to limit |

Savings goal ($1,200 flight home, $300 saved, 10 weeks, $81.22/week surplus):

```
You still need $900.00.
To finish in 10 weeks, save $90.00 per week.
You're $8.78 per week short. At your current rate it would take 12 weeks.
```

Uploading `data/second_student.csv` instead changes every figure (that student spent $464 more than they earned), which shows the answers come from the data, not fixed text.

## Repository layout

```
finance_assistant.ipynb      the full project: six-step method, code, app and tests
data/
  sample_transactions.csv    a realistic month for one student
  second_student.csv         a different student, to show the results change with the data
  messy_transactions.csv     bad rows used to test error handling
README.md
requirements.txt
.gitignore
```

## Testing

Step 6 of the notebook has 16 groups of assert-based tests. Expected values come from the hand-worked example in Step 3. They cover normal cases, edge cases (exact limits, refunds, files with only income, goals already reached) and invalid input (missing columns, empty files, non-numeric amounts, zero weeks), plus a log of bugs found and fixed.

## Limitations

- Categories come from the CSV; the app does not auto-categorise transactions.
- The weekly surplus estimate assumes the file covers about one month (30 days).
- Penny is not a financial adviser and is told not to give investment, tax or credit advice.
