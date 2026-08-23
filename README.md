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

## The seven pages

| File | What it is |
|---|---|
| `index.html` | **Landing page.** Almost empty by design — a header and the navigation strip. Its job is to hand you on to one of the three dashboards. |
| `udderhealth.html` | **Udder health** — 4 tabs: herd and lactation stage, milk quality, udder/mastitis/SCC, milking routine & hygiene (incl. milk flow and bimodality). |
| `reproduction.html` | **Reproduction** — 4 tabs: scorecard, breeding efficiency, postpartum disease, cow records. |
| `economics.html` | **Economics** — 4 tabs: feed cost & efficiency (per-pen drill-down), milk flow & losses, IOFC & herd structure (incl. IOFC per cow per day), Wood's lactation curves (per-parity drill-down). **Added 2026-08-22.** |
| `feed.html` | **Feed evaluation** — 4 tabs plus a per-cow record at the foot; clicking any cow on any chart opens it. Tabs: ration & intake, nutrient balance (per cow, August), grouping, outcomes (feed efficiency, MUN, body condition). Every chart is SVG written at build time, so the pictures do not depend on JavaScript. **Added 2026-08-23.** |
| `cullinglist.html` | **Culling** — a ranked attention list, in English. Two scores per cow, udder /25 and reproduction /25, totalling /50; every rule prints the measured fact that earned its points, so any score explains itself. **Rebuilt 2026-08-23.** |
| `actionplan.html` | **Action plan** — 4 tabs: priority (a cost × impact heat map and a criteria scan grid), 180-day roadmap, the 15 actions in detail, evidence & gaps. The herd team's own problem list, costed and sequenced. **Added 2026-08-22.** |

**Percentages.** The page shows one only where the parts genuinely compose a whole — inside a
stacked column, or across a set of categories that partition something (the three parity
groups). A single series across months does not qualify: *"March was 15.5% of the year's feed
bill"* describes where the x-axis was cut, not March. Where a share is shown it is printed on
the chart when the segment can hold the text, and is always in the tooltip as well.

**Drill-downs never scroll you back to the top** — opening a pen or a parity keeps your place.

All seven carry the same **Home · Economics · Feed · Reproduction · Udder health · Culling ·
Action plan** navigation, so one click moves between any two. The order and the markup live in
`assets/nav.json` — the single place the strip is defined.

> ### Renamed 2026-08-12
> The dashboard that used to be `index.html` is now **`udderhealth.html`**. `index.html` is the
> new landing page. Old links and bookmarks to `index.html` still open — they now land on the
> front door rather than the dashboard.

> **This public repository holds the seven pages and this README, nothing else.** The
> build scripts, the master workbook, the raw farm records and the project wiki live in the
> working copy and are not published. Paths below that point outside these files (`master/`,
> `../wiki/`, `assets/`, `backups/`) refer to that working copy. The dashboards are entirely
> self-contained, so nothing here depends on them at runtime.

| In the working copy | What it is |
|---|---|
| `Dairy_Milk_Quality_CU_Farm_2569.xlsx` | The source workbook behind the udder-health dashboard — 14 sheets, ~17k live formulas, 7 charts. |
| `build_reproduction.py` | Generates `reproduction.html` from the master workbook. |
| `build_economics.py` | Generates `economics.html` from the master workbook. Holds the single `MILK_PRICE` switch — see below. |
| `build_landing.py` | Generates `index.html`, reading every headline figure from the master workbook. |
| `build_actionplan.py` | Generates `actionplan.html`. Holds the `ACTIONS` config block — the plan's judgement layer (urgency, cost band, impact score, owner, KPI, deadline), kept visible so it can be argued with and edited without touching the rendering. |
| `build_feed.py` | Generates `feed.html`. Holds the threshold registry — every cut-off on the page with its source code (ฟ farm · C Cornell · N NRC 2001 · ป adjusted, with the reason). |
| `build_cullingscore.py` | Generates `cullinglist.html`. Weights live in `cullscore.py`, where each rule returns its points AND the fact that earned them. |
| `svgkit.py` | Draws every chart as SVG text from Python, so no picture depends on a script running. |
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

### 3 · The milk price is a flat valuation rate, and feed is the only recorded cost

**฿21.25/kg**, from `raw/economics/ราคาอาหาร+ปริมาณน้ำนม.xlsx`. Checked on all 137 populated
daily rows: the rate is **exactly 21.25 on all four channels with no variation** — including on
milk that was discarded. **That makes it the farm's own valuation rate, not a co-op price
schedule**; a purchase price would move with fat and SCC, which range widely over these months.
Set in `MILK_PRICE` at the top of `build_economics.py`.

Cull revenue is still underivable: the exit register's `Exit weight` and `Exit price` columns
are **empty in all 77 rows**.

**Feed is the only cost stream recorded anywhere.** No labour, veterinary, drug, breeding,
bedding, utility or depreciation figure exists in any file. "Income over feed cost" is not
profit, and is always larger than it.

### 3b · The farm's own report does not reconcile

`raw/economics/Combined_Dashboards_Report_Modified.html` computes the same quantities.
**Milk income matches to the baht in all six of its months.** **Feed cost does not** — theirs
runs 5–13% below the purchase ledger, and no single unit price reproduces it from the
offered/refused log. Their headcounts (62/89/151 in January) match neither the feed sheets'
own pen counts (116) nor the herd inventory (140). Both sets are shown on the *IOFC & herd
structure* tab rather than one being quietly chosen.

### 4 · The voluntary waiting period is assumed, not known

Submission rate, heat detection rate and days-to-first-service all depend on it, and no source
states the farm's policy. **45 days is assumed, provisionally**, pending confirmation from the
farm. The reproduction dashboard shows the assumption rather than burying it.

### The mastitis band labels changed on 2026-08-22

The udder-status chart's bands were renamed. **The bars did not move — only the labels did** —
but a figure quoted from an older screenshot will not mean what it says:

| Before | Now |
|---|---|
| New clinical | **Clinical** |
| New subclinical | **Subclinical** |
| Clinical | **Chronic clinical** |
| Subclinical | **Chronic subclinical** |

**"Chronic" here means only "she was infected at her previous test too".** It is *not* the
workbook's chronic flag (≥3 high SCC tests anywhere in the year, 24 cows) and *not* the
culling-list definition (≥3 **consecutive** tests, 7 cows). Three different meanings are in
circulation; always say which one a count came from.

### Milk flow and bimodality

Added 2026-08-22 to the *Milking routine & hygiene* tab, from the PM milking of 7 Aug 2026 —
the same 15 cows and the same day as the time-and-motion study beside it.

Flow is recorded in four windows (0–15, 15–30, 30–60, 60–120 s). **Every cow is plotted
individually**, one small panel each, so a curve can be read as unimodal or bimodal for that
animal rather than only in aggregate. The PM milking is the solid line; the AM milking of the
same day sits behind it as a faint dashed comparison.

| Pattern | n | Cows |
|---|---:|---|
| **Bimodal** — falls, then climbs again | **3** | 6503, 51009, 70117 |
| **Slow start** — first 15 s under 75% of her own peak | **8** | 6404, 6502, 7865, 51122, 61119, 750303, 750502, 750513 |
| Unimodal — one peak, clean rise and fall | 4 | 51226, 60703, 61219, 70212 |

**11 of 15 cows show one or the other**, and **the same three cows are bimodal in both
milkings** — a dip that repeats across two milkings on one day is a property of the cow and her
preparation, not noise in a single observation. Both patterns are what too little pre-stimulation produces, and this herd's pre-dip
contact averages **3.1 s against a 10–20 s target** — two instruments on the same cows on the
same day, agreeing.

**Read as a floor, not a census.** Four windows cannot resolve a dip shorter than 15 seconds,
so the true count is at least 3; and a window reading zero means the milking had finished, not
that flow stopped. Nothing here randomises prep time, so the association is
mechanistically expected but not proved; a prep-time change re-measured on the same cows would
settle it.

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
records · 4 years of breeding records, evaluated at 4 Aug 2026 · 212 days of milk-disposal
ledger and 7 months of feed cost, Jan–Jul 2026.

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

### Economics

| | Jan–Jul 2026 |
|---|---|
| Feed cost | **฿3,059,465** — the only recorded cost stream |
| Feed cost per kg sold to the co-op | **฿15.45**, and flat: ฿16.31 in Jan against ฿16.19 in Jul |
| Of that bill | **96.8% is two ingredients** — Betagro concentrate 59.6%, corn silage 37.2% |
| Feed efficiency | 1.67 (Jan) → 1.32 (Jun) → 1.59 (Jul) kg milk per kg DMI |
| Milk discarded | **9,303.7 kg**, 4.1% of accounted milk, rising 2.05% (Jan) → 7.55% (Jun) |
| Fed to calves | 18,241 kg — a transfer, not a loss |
| Milk income at ฿21.25/kg | ฿4,206,654 · **IOFC ฿1,147,189 (27.3%)** |
| **Milking cows as a share of animals fed** | **~40%** — and they eat **~73%** of the feed |
| Feed bill, July, by pen | นมมาก 31.2% · **สาว/heifers 25.3%** · นมกลาง 21.0% · นมน้อย 10.4% · ดราย 3.5% |
| Ration ladder, ฿/head/day | นมมาก 175 · นมกลาง 170 · นมน้อย 149 · **ดราย 149** — a 17% spread |
| Wood's 305-day yield | Lac 1 6,171 · Lac 2 7,127 · **Lac 3+ 5,850 kg — the lowest** |
| Healthy vs sick | **same peak (26.05 vs 26.37 kg)** — the loss is persistency: 62.2% vs 54.2% of peak held at 305 d |
| Subclinical ketosis | peaks **2.3 kg above the herd, 11 days earlier**, ends at **31.3% of peak** and **721 kg behind** |

**Feed efficiency did not deteriorate as production fell.** Feed cost fell 17.1% against milk
sold down 16.5%, so cost per kilogram moved 16.31 → 16.19, essentially unchanged. The farm
spent proportionally less as it produced less.

**The discard line is the one that moves.** It tracks clinical mastitis cases month for month,
both peaking in May–June, and 79% of treatments use a drug with a mandated milk withhold. At
the herd's own feed cost that milk represents **฿143,785 of feed already eaten**.

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
python3 master/build_master.py                        # the master workbook — run this first
python3 dashboard/build_landing.py                    # index.html
python3 dashboard/build_reproduction.py               # reproduction.html
python3 dashboard/build_economics.py                  # economics.html
python3 ../tools_add_mastitis_epi.py  <source.xlsx> <output.xlsx>
python3 ../translation/apply_translation.py ../translation <source.html> <output.html>
```

`reproduction.html`, `index.html` and `economics.html` are **derived artefacts and are never
hand-edited** — edit the generator and rebuild. All three rebuild byte-identically from
unchanged inputs. `udderhealth.html` is still maintained by hand, which is why adding a fourth
navigation entry required patching it directly; the other three read `assets/nav.json`.

The workbook must **never** be opened and re-saved with openpyxl — it does not read charts or
drawings, so a round-trip silently destroys all 7 charts. Both workbook scripts edit the OOXML
inside the zip directly and verify the chart parts byte-for-byte afterwards.

## Deliberately not in this folder

- `../Lactation_Dashboard_CU_Farm_2569.html` — the lactation-curve dashboard. Left in place
  because `Lactation curve for Claude/rebuild/verify_states.py` and `assemble.py` open it by
  relative path; moving it would break that pipeline. It is still in Thai.
- `../DIM_MilkYield_Correlation_byParity_2569.png` / `.pdf` — the DIM × yield publication
  figure, kept beside its build script and spec.
