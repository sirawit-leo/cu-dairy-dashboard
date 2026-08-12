# CU Research Dairy Farm — Dairy Herd Monitoring dashboards

> ## For academic discussion and presentation only — not for sale or redistribution
>
> The data presented here were collected by the staff of the **Chula Dairy Research Farm,
> Faculty of Veterinary Science, Chulalongkorn University**, in the course of their routine
> work on the herd. They are shared for teaching, discussion and conference presentation.
> Do not sell, redistribute or republish them, in whole or in part, without the farm's
> permission. Any figure quoted elsewhere must carry its n and its date.

**Open `index.html`** in any browser. Every page is fully self-contained: all data is
embedded, no internet, no server, no external files. Double-clicking works, and so does
copying this whole folder to a USB stick or sharing it.

## The three pages

| File | What it is |
|---|---|
| `index.html` | **Landing page.** Almost empty by design — a header and the navigation strip. Its job is to hand you on to one of the two dashboards. |
| `udderhealth.html` | **Udder health** — 4 tabs: herd and lactation stage, milk quality, udder/mastitis/SCC, milking routine & hygiene. |
| `reproduction.html` | **Reproduction** — 4 tabs: scorecard, breeding efficiency, postpartum disease, cow records. |

All three carry the same **Home · Udder health · Reproduction** navigation, so one click moves
between any two.

> ### Renamed 2026-08-12
> The dashboard that used to be `index.html` is now **`udderhealth.html`**. `index.html` is the
> new landing page. Old links and bookmarks to `index.html` still open — they now land on the
> front door rather than the dashboard.

> **This public repository holds the three dashboards and this README, nothing else.** The
> build scripts, the master workbook, the raw farm records and the project wiki live in the
> working copy and are not published. Paths below that point outside these files (`master/`,
> `../wiki/`, `assets/`, `backups/`) refer to that working copy. The dashboards are entirely
> self-contained, so nothing here depends on them at runtime.

| In the working copy | What it is |
|---|---|
| `Dairy_Milk_Quality_CU_Farm_2569.xlsx` | The source workbook behind the udder-health dashboard — 14 sheets, ~17k live formulas, 7 charts. |
| `build_reproduction.py` | Generates `reproduction.html` from the master workbook. |
| `build_landing.py` | Generates `index.html`, reading every headline figure from the master workbook. |
| `assets/` | Brand logos, and the shared page shell + navigation the two generators read. |
| `backups/` | Every prior version, timestamped. Nothing has been deleted. |

The dashboards do **not** read the workbook at runtime, so opening or editing the workbook
cannot break a dashboard, and vice versa.

---

## Read this before quoting any number

Three traps account for most of the ways these figures can be misread. Each is documented in
full under `../wiki/limitations.md`.

### 1 · The 81-day milk-recording outage (27 Apr – 16 Jul 2026)

No milk yield was recorded for any cow across that window. **Per-cow peak yield, peak day and
persistency are artefacts for 20 of 63 cows** — 10 have their peak inside the gap, 10 are
truncated at the start. Body weight stopped at the same moment and never came back. Anything
derived from a lactation curve must be read against this first.

### 2 · Transposed dates in the reproduction workbook

The herd-evaluation file was exported as `dd/mm/yyyy` text and read back by a parser expecting
`mm/dd`, so every date whose day was 12 or under had its day and month swapped. **374 dates
were repaired against independent evidence and 36 dropped as unresolvable**; the manifest is
master sheet `ReproDateRepairs`.

Consequence: **do not quote a reproductive interval from the source workbook's own cells.**
Days to first service reads 88.7 d there and **73.9 d** recomputed. Use `reproduction.html`.

A second consequence: heat and veterinary records in that file **begin only in 2025** — zero
events of either for 2021–2024. Any pre-2025 trend in conception rate, heat detection or
disease incidence measures how much was written down, not how the herd performed. Only calving
interval and calving index are safe to trend across four years.

### 3 · The voluntary waiting period is assumed, not known

Submission rate, heat detection rate and days-to-first-service all depend on it, and no source
states the farm's policy. **45 days is assumed, provisionally**, pending confirmation from the
farm. The reproduction dashboard shows the assumption rather than burying it.

### Smaller ones, still worth knowing

- **No drying-off date exists anywhere.** DPNIR and DPCURE substitute the cow's last recording
  of her previous lactation. Recording a dry-off event is the single highest-value data change
  available.
- **Dystocia 0% is a recording artefact**, not a result — the workbook has no calving-ease
  field at all.
- **Any rate whose denominator is under 20 is flagged on the page.** Five of the six postpartum
  conditions have a numerator under 20 cows and are shown as counts, not percentages.
- **Hygiene and teat-end scores are one-off snapshots** (23 Jun and 7 Aug 2026), so the month
  filter does not change that tab.

---

## Data covered

15 milk-recording rounds, 6 Jan – 4 Aug 2026 · 812 individual samples from 78 cows ·
15 bulk-tank rounds · 79 culture isolates · 185 ketone (BHBA) values · 1,518 veterinary
records · 4 years of breeding records, evaluated at 4 Aug 2026.

## What to look at first

### Udder health

The workbook's **`Mastitis Epidemiology`** sheet carries the five standard udder-health
parameters, monthly plus a whole-period row.

| Parameter | Period value | Target | |
|---|---|---|---|
| LNIR — lactation new infection rate | 5.8% (n=534) | <5%/month | marginally above |
| **CML — clinical mastitis, lactation origin** | **72.4 /100 cows/yr** (26 cases) | <16.66 | **≈4× target** |
| CMDP — clinical mastitis, dry-period origin | 11.1 /100 cows/yr (4 cases) | <8.33 | above |
| DPNIR — dry period new infection rate | 14.3% (n=7) | <10%/month | ⚠ too few cows |
| DPCURE — dry period cure rate | 100% (n=3) | — | ⚠ too few cows |

**Clinical mastitis is running about four times target**, and 40% of the herd (31 of 78 cows)
had a first case within 8 months. The evidence points to environmental infection between
milkings, not a milking-plant problem — bulk-tank hygiene passed every round (TPC 370–6,500
against a 100,000 limit), the pathogen mix is dominated by environmental streptococci, cases
peak at 101–200 DIM, and the case curve rises into the wet season.

Culling the 11 chronic cows would **not** fix the rate — it moves CML from 72.4 to 73.3,
because those cows remove both cases and cow-numbers together. They are an antimicrobial-use
problem (60% of all treatments), not the incidence driver.

### Reproduction

Every parameter is recomputed from raw events, with n and formula on the page.

| Parameter | Recomputed | Target | Workbook said |
|---|---|---|---|
| Days to first service | **73.9 d** (n=46) | <65 | 88.7 |
| Calving to conception | 139.1 d (n=22) | 90–110 | 144.2 |
| Calving interval | 403.0 d (n=34) | ≤365 | 411.5 |
| Heat detection rate | **30.0%** | >75 | `#REF!` / 55.7 |
| Conception rate | 39.3% (22/56) | 55 | 30.9 |
| Endometritis | **26 cows, 21–41%** | <10% | 25.5% |

**Endometritis is the largest reproductive problem in this herd** — roughly four times target,
alongside a first-service conception rate of 28.9%, which is what an untreated uterine-infection
load predicts.

## Editing thresholds

Every udder-health cut-off lives on the workbook's **`Thresholds`** sheet and every formula
reads from it — change a value and the whole workbook recalculates. Row 3 holds the 200,000
cells/ml subclinical cut-off; rows 51–61 hold the epidemiology constants (400k post-calving
cut-off, the 5-day and 30-day windows, DIM 31/305 split, the four targets, and the minimum-n
of 20).

## Regenerating

```
python3 dashboard/build_landing.py                    # index.html
python3 master/build_master.py                        # the master workbook
python3 dashboard/build_reproduction.py               # reproduction.html
python3 ../tools_add_mastitis_epi.py  <source.xlsx> <output.xlsx>
python3 ../translation/apply_translation.py ../translation <source.html> <output.html>
```

`reproduction.html` and `index.html` are **derived artefacts and are never hand-edited** — edit
the generator and rebuild. Both rebuild byte-identically from unchanged inputs.
`udderhealth.html` is still maintained by hand.

The workbook must **never** be opened and re-saved with openpyxl — it does not read charts or
drawings, so a round-trip silently destroys all 7 charts. Both workbook scripts edit the OOXML
inside the zip directly and verify the chart parts byte-for-byte afterwards.

## Deliberately not in this folder

- `../Lactation_Dashboard_CU_Farm_2569.html` — the lactation-curve dashboard. Left in place
  because `Lactation curve for Claude/rebuild/verify_states.py` and `assemble.py` open it by
  relative path; moving it would break that pipeline. It is still in Thai.
- `../DIM_MilkYield_Correlation_byParity_2569.png` / `.pdf` — the DIM × yield publication
  figure, kept beside its build script and spec.
