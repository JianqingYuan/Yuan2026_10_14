# ER/RN orbital comparison

[中文](README.zh-CN.md)

Companion code for *Nearly indistinguishable: charged black holes in general
and entangled relativity*.

## Start here

The program is in `Yuan2026_10_14` (distributed on GitHub as
`Yuan2026_10_14.zip`). Extract the ZIP if needed. With Python 3.12 installed,
open Command Prompt in the folder containing `main.py` and `requirements.txt`,
then run:

```console
py -3.12 -m venv .venv
.venv\Scripts\activate
python -m pip install -r requirements.txt
python main.py
```

The default redraws 13 paper figures from saved data, without fitting.
Open `Yuan2026_10_14/output/paper_<timestamp>/figures/` for the PDFs and PNGs.
The saved data include the reviewed four S301 pairs and 77 extreme ER
corrections used in revised Figures 13 and 12; other archived values are retained.

To calculate a live demonstration instead, run `python main.py --mode demo`.
It plans 42 model fits with finite budgets: some results may need review or
remain missing. It does not use saved paper fits to fill gaps, and is not a
fresh verification of the paper's statistical conclusions. Full recomputation
is a separate, substantially larger calculation.

## Details and evidence

After extraction, open these files inside `Yuan2026_10_14`:

- `README.md`: installation, modes and optional advanced settings.
- `AUDIT_STATUS.md`: audit scope and limitations.
- `VALIDATION.json`: validation and data identities.

Separate attachment:

- [Original selected-endpoint evidence](audit_evidence_20261015.zip)

The evidence ZIP is a separate audit record, not another copy of the program.
The targeted corrections and software checks do not prove global optimality
or revalidate every historical fit. No additional user settings are required
for the default redraw.
