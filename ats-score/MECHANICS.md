# ATS mechanics

Reference for [`ats-score`](SKILL.md): how to inspect a resume file, the parseability
checklist, what each platform does differently, and the section rubric.

## Inspecting the real file

Half the parseability tests are invisible in pasted text. Get them from the file:

**PDF** — `Read` the file with `pages` set; the pages come back visually, which is how you
see columns, icons, logos, decorative bullets and non-standard fonts. A page that renders as
an image with no selectable text is a **scanned PDF**: total parse failure, and the single
most expensive defect on this list.

**DOCX** — it is a zip:

```bash
unzip -o resume.docx -d /tmp/ats && ls /tmp/ats/word
textutil -convert txt -stdout resume.docx    # what a plain text extractor gets
```

Then grep `/tmp/ats/word/document.xml`:

| Looking for | Grep |
|---|---|
| Tables | `<w:tbl>` |
| Text boxes / shapes | `<w:txbxContent>`, `<wps:` |
| Multi-column layout | `<w:cols w:num="2"` |
| Images | `ls /tmp/ats/word/media/` |
| Header/footer content | `ls /tmp/ats/word/header*.xml footer*.xml` |
| Fonts in use | `<w:rFonts` |

The `textutil` output is the closest cheap proxy for what a literal-matching ATS sees. If the
phone number, email or a job title is missing from it, that field is unparseable — regardless
of how clearly it reads on screen.

## Parseability checklist

Score `/10`. Mark `❓ needs the file` for any test whose source column says *file* when only
pasted text was supplied.

| # | Test | Source | Fix when it fails |
|---|---|---|---|
| 1 | Contact info extractable (name, phone, email, city) | file | Move it into the body as plain text lines, one item per line |
| 2 | Standard section headings | text | Rename to the literal words `Experience`, `Education`, `Skills`, `Certifications` — creative headings ("Where I've Been") land in no field |
| 3 | Dates beside job titles | text | Put `Title — Company — Mon YYYY–Mon YYYY` on the role line, not in a side column |
| 4 | No tables, columns or text boxes | file | Rebuild as a single-column linear document; text boxes are frequently dropped entirely |
| 5 | No images, icons, logos or skill bars | file | Delete them; a skill rated as a 4/5 bar conveys nothing to a parser |
| 6 | Standard font (Arial, Calibri, Helvetica, Times, Garamond) | file | Swap the display font; embedded/decorative fonts can extract as garbage glyphs |
| 7 | No content in headers or footers | file | Move it into the body — many parsers never open the header |
| 8 | Consistent date format throughout | text | Pick one (`Mar 2023`) and use it everywhere, including education |
| 9 | Standard bullet glyphs (`•`, `-`) | text | Replace ornaments (`▸ ✦ ➤`) — they can merge into the following word |
| 10 | Clean section separation | text | One blank line between sections, no horizontal rules or borders |

**Kill list** — any one of these can sink an otherwise strong resume: scanned/image-based PDF,
tables, text boxes, multi-column layout, content living only in a header or footer,
decorative fonts, skill-rating graphics.

## Platform behaviour

Cite the ones that matter for the resume in hand.

| Platform | Used by | Behaviour that matters here |
|---|---|---|
| Workday | Amazon, Netflix, Walmart | Strictest format parsing; punishes tables and columns hardest |
| Taleo | Deloitte, Boeing, HSBC | Literal keyword matching — synonyms earn nothing, so acronym *and* full form both need to appear |
| Greenhouse | Airbnb, Slack, Stripe | Contextual parsing; a keyword inside an achievement outranks one in a list |
| Lever | Netflix, Shopify, Lyft | Light parsing, human-forward; format sins cost less here |
| iCIMS | Target, UPS, Hilton | Field-based extraction; unlabelled sections lose their contents |
| BambooHR | SMBs | Basic keyword scan |
| SAP SuccessFactors | Samsung, Siemens | Enterprise strict mapping; non-standard headings fail to map |
| SmartRecruiters | IKEA, Visa, LinkedIn | AI-driven scoring; closest to semantic matching |

**Match types** — literal (exact string) works everywhere; contextual (keyword inside an
achievement) works on modern parsers; synonym and semantic matching work only on the AI-driven
ones. Write for the literal case and the rest follow: state the acronym and the full form once
each, `RAG (retrieval-augmented generation)`.

## Section rubric

Total `/100`, scored independently of the match score. Rewrite any section below 70% of its max.

**Contact information — 5.** Name, phone, professional email, city/country, LinkedIn.
+1 for a custom LinkedIn slug or a portfolio/GitHub link. Anything in a header: 0.

**Professional summary — 15.** 3–4 lines. Carries the target job title verbatim, the years of
experience, and 3+ 🔴 keywords. Deduct for objective-style phrasing ("seeking a role where I
can grow") — it spends the highest-weighted real estate on the page saying nothing.

**Work experience — 30.** Per role: strong action verb opening every bullet, a number in the
majority of them, 🔴 keywords used in achievement context, and relevance to the target role.
Deduct for duty-listing ("Responsible for…"), for bullets over three lines, and for a role
with zero quantified outcomes.

**Education — 10.** Degree, institution, year. Relevant coursework only if experience is thin.

**Skills — 20.** Grouped into named categories (Languages / Frameworks / Cloud & DevOps /
Tools), every 🔴 skill they genuinely hold present, acronym and full form both given. Deduct
for one undifferentiated comma blob, and for soft skills occupying space a parser will not
reward.

**Certifications — 10.** Name, issuer, year, and current validity. Score 10 if none exist and
the JD asks for none; score against the JD's named certs where it does.

**Formatting & ATS compliance — 10.** Length appropriate to experience (1 page under 10 years,
2 acceptable beyond), consistent tense, no typos, `.docx` or text-based `.pdf`, standard
headings. This mirrors the parseability audit at document level — cite it rather than
re-deriving it.
