# Entry test: Google Sheets

My starting-point check before Stage 1. Done on 2026-11-03 in Google Sheets.

**Data:** [`hiring_funnel.csv`](hiring_funnel.csv), 60 candidates (July–September 2026) with position, source, recruiter, application date, funnel stage, days in process and salary expectation.

**Scope:** the short version (tasks 1–5) plus task 6. Tasks 7–11 (pivot, funnel conversion, best source, `QUERY`, chart) are planned for the spreadsheet practice week.

> Formulas use `;` as the argument separator (Ukrainian locale).

## Results

| # | Task | Result | How |
|---|---|---|---|
| 1 | Count candidates and hires | 60 candidates, 8 hired | `COUNTA` on the ID column (from row 2, without the header), `COUNTIF` on `stage_reached` |
| 2 | Mean and median salary expectation | Mean 2649, median 2500 | `AVERAGE(I2:I61)`, `MEDIAN(I2:I61)` |
| 3 | Candidates per source | LinkedIn 21, Djinni 14, Work.ua 9, DOU 8, Referral 8 (sum = 60) | `COUNTIF` per source |
| 4 | Find dirty data | 3 problems found, see below | `COUNTBLANK` per column, `UNIQUE` |
| 5 | Lookup by `candidate_id` | Input cell + formula returns position | `=XLOOKUP(U2; A2:A61; B2:B61; "Не найден")` |
| 6 | Month of application | Jul 22, Aug 18, Sep 20 | Two ways, cross-checked (below) |

## Notes

**Mean vs median.** A few high expectations (4000–4400 USD) pull the mean up, while the median only depends on the middle value. For salaries and time-to-hire the median is the better "typical" number, because single outliers (one senior candidate, one vacancy open for 200 days) distort the mean.

**Dirty data and how I would fix it.**
1. `source` for C023 is `linkedin ` (lowercase + trailing space), so `COUNTIF("LinkedIn")` misses it. Fix it once in the dataset (Find & Replace, or `TRIM` + `PROPER` on a large table) instead of patching every formula.
2. `salary_expectation_usd` is empty for C008. Keep the candidate in the funnel, exclude the value from salary statistics (`AVERAGE`/`MEDIAN` skip blanks). Never fill it with 0.
3. `days_in_process` is empty for C041 (stage Screening). Ask the recruiter for the real value; 0 would be wrong for a candidate who already passed Applied.

**Counting by month, two ways.**
- Helper column `Month` = `MONTH(E2)` copied down, then `COUNTIF` on it.
- Without a helper column: `=COUNTIFS($E$2:$E$61; ">="&DATE(2026;9;1); $E$2:$E$61; "<"&DATE(2026;10;1))`

Both gave the same numbers. Computing a figure two ways caught one mistake (wrong month in one formula).

## What I learned

- `:` is a range (`I2:I61`), `;` separates arguments (`I2;I61` is just two cells).
- `DATE` takes year; month; day. In criteria only the operator goes in quotes: `">="&DATE(...)`.
- "Less than the 1st of next month" is safer than "≤ last day" (September has 30 days).
- A formula's result appears in the cell where it is written; a formula must not refer to its own cell (circular dependency).
- `XLOOKUP` joins data by a key. In SQL this will be `JOIN`.
