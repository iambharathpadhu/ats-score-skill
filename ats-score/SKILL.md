---
name: ats-score
description: Simulate how a real ATS parses and ranks a resume against one job description — pillar-weighted match score out of 100, counted keyword gaps, a parseability audit of the actual file, section scoring, and rewrites of every weak section. Use when the user wants a resume scored or ATS-optimised against a JD, asks why a resume keeps getting filtered out, or types /ats-score.
---

# ATS score

Simulate what Workday, Taleo, Greenhouse, Lever, iCIMS and SmartRecruiters actually do to a
resume: extract fields, count keywords, rank against one JD. An ATS is a **counter**, not a
reader — it scores what is literally on the page in a field it managed to parse.

So every number you report is **counted**, and every count is shown. A score the user can't
trace back to a keyword list is a guess dressed as a measurement, and it costs them the
interview it flattered them into skipping.

Read [`MECHANICS.md`](MECHANICS.md) before step 3 — it holds the parseability checklist, how
to inspect the real file, per-platform behaviour, and the section rubric.

## Step 1 — Parse the resume

Ask for the **path to the actual file** (`.pdf` / `.docx`), not pasted text. The file is the
only thing that answers half the parseability audit.

Read it, then echo back what parsed out: name, current title, years of experience, top 5–7
skills. Any field you could not extract is itself the first ATS finding — report it as one.

If the user only has pasted text, proceed: keyword and content scoring work fine. Mark every
format test that needs the binary as `❓ needs the file`. **Unknowable is not a pass.**

Done when the user confirms the parse. Then ask for the JD and wait — no scoring until both
inputs are in hand.

## Step 2 — Extract the JD keyword set

Do this **before** re-reading the resume. Extracting keywords after reading the resume
anchors you to what is already there, and the missing-keyword list is the deliverable.

Record the job title and company, then list every keyword the JD contains, each tagged:

| Tag | Meaning |
|---|---|
| 🔴 Critical | In the requirements/qualifications block, or repeated 3+ times |
| 🟡 Important | Named once, in responsibilities |
| 🟢 Bonus | Under "nice to have" / "preferred" |

Done when the list is numbered and every entry carries a tag. That total is `Y` for the rest
of the run.

## Step 3 — Score the four pillars

This is the **match score /100** — the headline, and the only score out of 100 you sum.

| Pillar | Max | How to score it |
|---|---|---|
| 🔑 Keyword density & context | 35 | `(keywords found / Y) × 35`, then subtract up to 5 for keywords that appear only in a skills list with no achievement context |
| 🏷️ Job title relevance | 25 | Exact title match 25 · adjacent title (same function, different seniority or naming) 15–19 · related function 8–13 · unrelated 0–5 |
| 🛠️ Skills alignment | 25 | `(required skills present / required skills in JD) × 25`. Count 🔴 and 🟡 only; 🟢 skills cannot raise this above its ceiling |
| ⏱️ Recency & experience | 15 | Weight each matching role by age: last 3 years ×1.0 · 3–7 years ×0.6 · 7+ years ×0.3. Flag a seniority mismatch against the JD's stated years |

Rating: **85–100** 🟢 strong match · **70–84** 🟡 good match · **50–69** 🟠 at risk of being
filtered · **0–49** 🔴 likely rejected.

Every pillar line in the report carries its arithmetic — `18/35 (9 of 24 keywords found, −1 context)`.

## Step 4 — Audit parseability

Run the 10 tests in [`MECHANICS.md`](MECHANICS.md) against the file, using the inspection
commands there rather than assuming. Score `/10`.

This is a **gate, never a summand**. Below 7, open the report with it: a match score assumes
the ATS read the fields, and at that parseability the fields may never reach the ranker at all.

Name the platforms this resume survives and the ones it fails, from the platform table.

## Step 5 — Score the sections

Run the section rubric in [`MECHANICS.md`](MECHANICS.md) — contact, summary, experience,
education, skills, certifications, formatting. This is a **separate diagnostic /100** of
resume quality; it is never added to the match score.

Each section gets its points, its strengths, and its issues — each issue naming the specific
line or the specific missing keyword, so the user can act on it without asking a follow-up.

## Step 6 — Rewrite every section under 70%

Any section scoring below 70% of its max gets a Before / After / Changes-made block.

Rewrite **only from what is on the page**: their roles, their metrics, their tools. Where a
🔴 keyword has no basis in their experience, write the line as a prompt they can accept or
delete — `Add if true: "…"` — so they supply the truth and you supply the phrasing.

Where a bullet states a duty, convert it to an achievement: action verb + what changed + the
number. Where no number exists on the page, mark the slot `[metric?]` rather than inventing one.

Done when every section below the bar has a rewrite. Sections that scored above it are left
alone and said to be left alone.

## Step 7 — Project the after-score

Re-run step 3's arithmetic against the **rewritten** text and show before → after per pillar.
Credit a pillar only for keywords the rewrite literally contains — an `Add if true:` line the
user hasn't accepted yet earns nothing, and a projection that counts it is the same inflation
this skill exists to prevent.

Close by offering: the fully optimised resume assembled end to end, or the same resume against
a different JD.

## Report format

Render every table as a GitHub-flavoured markdown table — the terminal renders those and the
columns stay aligned at any content width.

Report in this order: parseability gate (if under 7) → match score with the pillar table →
keyword breakdown → parseability detail → section scores → rewrites → projection.

The keyword breakdown is two tables. **Found**: keyword, frequency, which section it sits in.
**Missing**: keyword, tag, and exactly where to add it. Every one of the `Y` keywords appears
in exactly one of the two — the two counts sum to `Y`, and the match rate is stated as
`X/Y = Z%`.
