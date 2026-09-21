# $0 Bioelectronics + Bioimaging Daily Literature Tracker

A no-paid-API literature monitoring pipeline for **bioelectronics + bioimaging** using GitHub Actions.

It checks **Semantic Scholar + Crossref + arXiv** every day, merges duplicate papers, remembers what has already been seen, applies a transparent keyword relevance score, and saves a Markdown + CSV report. If relevant new papers are found, GitHub opens one daily Issue as the notification.

**No OpenAI API is used. No paid service is required.**

## Topics already configured

The included `config.yml` is intentionally broad around materials/device-oriented bioelectronics and optical/molecular bioimaging:

- organic / soft / stretchable bioelectronics
- implantable and wearable bioelectronics
- biointegrated electronics
- neural interfaces, neural recording, neural stimulation, electrophysiology
- organic electrochemical transistors (OECTs)
- organic mixed ionic-electronic conductors (OMIECs)
- conducting polymers and PEDOT:PSS
- iontronics / ionic-electronic interfaces
- conductive hydrogels and soft electrodes
- bioelectronic sensors
- epidermal electronics / electronic skin
- brain-machine / brain-computer interfaces
- peripheral-nerve interfaces and bioelectronic medicine
- fluorescence / molecular / optical bioimaging
- organic fluorophores and fluorescent probes
- TADF emitters for bioimaging
- NIR / NIR-II fluorescence imaging
- two-photon fluorescence imaging
- fluorescence lifetime imaging (FLIM)
- super-resolution and live-cell imaging
- neural, calcium, voltage, and membrane-potential imaging

The first run searches the previous **7 days**. Later runs use a **40-hour overlap window**, while `state/seen.json` prevents duplicate notifications.

## Setup

### 1. Create a second GitHub repository

Suggested name:

`bioelectronics-bioimaging-paper-tracker`

A public repository is simplest for staying at $0.

### 2. Upload the contents of this folder to the repository root

Upload the **contents**, not the outer folder itself. The repository should look like:

```text
bioelectronics-bioimaging-paper-tracker/
├── .github/
│   └── workflows/
│       └── daily.yml
├── reports/
│   └── .gitkeep
├── state/
│   └── seen.json
├── tracker.py
├── config.yml
├── requirements.txt
├── README.md
├── .gitignore
└── LICENSE
```

### 3. Commit and publish

Using GitHub Desktop:

1. Put the files into the local repository folder.
2. Commit them (for example: `Add bioelectronics literature tracker`).
3. Publish the repository.

### 4. Run once manually

On GitHub:

**Actions → Daily bioelectronics + bioimaging literature tracker → Run workflow → Run workflow**

The first run searches the last 7 days.

### 5. Daily behavior

The workflow runs at **13:30 UTC** every day (about 8:30 AM Chicago during daylight-saving time / 7:30 AM during standard time).

Each run:

```text
Semantic Scholar ─┐
Crossref ──────────┼─> merge/deduplicate
arXiv ─────────────┘
                         ↓
                  remove seen papers
                         ↓
                  keyword relevance score
                         ↓
               reports/YYYY-MM-DD.md
               reports/YYYY-MM-DD.csv
                         ↓
              one GitHub Issue if nonempty
```

## Where to read the papers

The easiest place is the repository's **Issues** tab. One Issue is created for each day that has at least one new paper passing the filter.

For the complete archive, open:

**Code → reports → YYYY-MM-DD.md**

The CSV version is also saved for spreadsheet-style sorting/filtering.

## Adjusting what counts as relevant

Edit `config.yml`.

- Add/remove database searches under `queries`.
- Raise keyword weights for your highest-priority areas.
- Raise `min_score` if the reports are too noisy.
- Lower `min_score` if useful papers are being missed.

For example, to focus heavily on neural OMIEC/OECT work, increase weights for:

```yaml
"organic electrochemical transistor": 12
"omiec": 12
"neural interface": 10
```

## Optional Semantic Scholar key

Not required. If you later get a free Semantic Scholar API key, add it as a repository secret named:

`SEMANTIC_SCHOLAR_API_KEY`

Do not put API keys directly in `config.yml`.

## Important limitation

No single literature database covers literally every paper. This tracker improves recall by taking the union of three sources, but indexing delays, missing abstracts, incomplete metadata, and source coverage can still cause omissions.
