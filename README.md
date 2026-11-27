# ER/RN orbital comparison

[中文](README.zh-CN.md)

Companion code for *Nearly indistinguishable: charged black holes in general
and entangled relativity*.

For the version distributed with the paper, download [Yuan2026_10_14.zip](https://github.com/JianqingYuan/Yuan2026_10_14/blob/55964c9a82394d44472c3e39c46ee89943c786bc/Yuan2026_10_14.zip) and follow the [original instructions](https://github.com/JianqingYuan/Yuan2026_10_14/blob/55964c9a82394d44472c3e39c46ee89943c786bc/README.md).

## Current release

Download the package you need from the **Assets** section of the [GitHub release](https://github.com/JianqingYuan/Yuan2026_10_14/releases) and extract it.

- `Yuan2026_11_07_general_spherical_v9_4_B4.zip` contains the B4 orbital fitting code. Start with `README.md` inside the archive. The B4 fitting guide is `README_NEXTGEN.zh-CN.md` (in Chinese).
- `periapsis-profile-0.1.1.zip` computes the uncertainty in the pericentre advance per radial period from an existing fit. It accepts a B4 fit or your own fit and can be installed independently. Its `README.md` includes a worked RN example (case 36).

To redraw the paper figures with B4, use the paper entry point in the B4 directory. With Python 3.12 installed,
open Command Prompt in the folder containing `main.py` and `requirements.txt`,
then run:

```console
py -3.12 -m venv .venv
.venv\Scripts\activate
python -m pip install -r requirements.txt
python main.py
```

The default redraws 13 paper figures from saved data, without fitting.
Open `Yuan2026_11_07_general_spherical_v9_4_B4/output/paper_<timestamp>/figures/` for the PDFs and PNGs.
The saved data include the reviewed four S301 pairs and 77 extreme ER
corrections used in revised Figures 13 and 12; other archived values are retained.

To calculate a live demonstration instead, run `python main.py --mode demo`.
It plans 42 model fits with finite budgets: some results may need review or
remain missing. It does not use saved paper fits to fill gaps, and is not a
fresh verification of the paper's statistical conclusions. Full recomputation
is a separate, substantially larger calculation.

## Details and evidence

Open these files inside `Yuan2026_11_07_general_spherical_v9_4_B4`:

- `README.md`: installation, modes and optional advanced settings.
- `AUDIT_STATUS.md`: audit scope and limitations.
- `VALIDATION.json`: validation and data identities.

Separate attachment:

- [Original selected-endpoint evidence](https://github.com/JianqingYuan/Yuan2026_10_14/blob/55964c9a82394d44472c3e39c46ee89943c786bc/audit_evidence_20261015.zip)

The evidence ZIP is a separate audit record, not another copy of the program.
The targeted corrections and software checks do not prove global optimality
or revalidate every historical fit. No additional user settings are required
for the default redraw.
