# ats-score — a Claude Code skill that scores your resume the way an ATS does

An Applicant Tracking System (Workday, Taleo, Greenhouse, Lever, iCIMS…) is a **counter, not a
reader**. It extracts fields from your resume file, counts the job description's keywords, and
ranks you. Two-column layouts, contact details in the header, skill bars and icons are all
garbage to it.

This skill makes Claude Code simulate that: give it your resume file and one job description, and
it returns

- a **match score out of 100**, pillar by pillar, with the arithmetic shown
- every keyword it **found** and every keyword **missing**, with where to add it
- a **10-test parseability audit** of the actual `.pdf`/`.docx`
- rewrites for every weak section, built only from what is already on the page

Every number is counted, never guessed. If a score can't be traced to a keyword list, the skill
doesn't report it.

## Install

```bash
mkdir -p ~/.claude/skills
git clone https://github.com/iambharathpadhu/ats-score-skill.git /tmp/ats-score-skill
cp -r /tmp/ats-score-skill/ats-score ~/.claude/skills/ats-score
```

Then in Claude Code:

```
/ats-score
```

It asks for the path to your resume file, echoes back what it parsed, then asks for the JD.

## Files

| File | What it holds |
|---|---|
| `ats-score/SKILL.md` | The seven-step workflow and the scoring rubric |
| `ats-score/MECHANICS.md` | The parseability checklist, how to inspect the real file, per-platform behaviour |

## Honest limits

It simulates ATS behaviour from documented patterns. It is not any vendor's actual ranking code.
Use it to fix what is obviously broken, not to chase a number.

---

Built by a frontend engineer who explains tech in Tamil — *Barath | Compile & Compound* on
Instagram and YouTube.
