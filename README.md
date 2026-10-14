# ER/RN orbital comparison

[中文说明](README.zh-CN.md)

## 1. Physical setting

Companion code for *Nearly indistinguishable: charged black holes in general
and entangled relativity*. We compare neutral test-particle and light motion
in static, spherically symmetric charged spacetimes: entangled relativity
(ER) and the Reissner–Nordström (RN) solution of general relativity.

The time-series experiments use idealised planar orbits generated in ER,
with and without Gaussian noise. ER and RN fit the same positions, each
adjusting six source/orbit parameters. DOP853 integrates the motion;
differential evolution (DE) and trust-region reflective least squares (TRF)
perform the fits.

## 2. Package structure

- `main.py`: launches calculations and plotting.
- `settings.py`: the settings readers normally change.
- `engine/`: a common interface to the old and new fitting workflows.
- `vendor/`: preserved orbit integration and DE/TRF workflow code.
- `experiments/` and `configs/`: dataset selection and experiment definitions.
- `data/observations/`: input positions for new fits.
- `data/paper/`: saved numerical results for redrawing the manuscript figures.
- `plotting/`: figure generation; `tests/`: regression tests.
- `output/`: new results and figures, created when running.

Further details: [experiment notes](experiments/README.txt),
[saved-data format](data/paper/SCHEMA.txt), and [validation record](VALIDATION.json).

## 3. How to run the code

### First use on Windows

**Step 1: Extract the entire code package.**
Open the extracted folder containing both
`main.py` and `requirements.txt`.

**Step 2: Install Python 3.12.** Skip this step if it is already installed.
See the official
[Python Windows installation instructions](https://docs.python.org/3.12/using/windows.html).
Include the Python launcher (`py`) and pip.

**Step 3: Prepare a Python environment for this package (first use only).**
In the package folder window, click the address bar at the top, type `cmd`,
and press Enter. This opens Command Prompt in that folder. Enter these lines
one at a time:

```console
py -3.12 -m venv .venv
.venv\Scripts\activate
python -m pip install -r requirements.txt
```

Wait for installation to finish.

**Step 4: Generate the figures.** In the same Command Prompt window, enter:

```console
python main.py
```

With `MODE = "paper"` in `settings.py`, this redraws all 13 manuscript figures
from saved numerical results, without repeating the fits. If you changed
`MODE` to `"demo"`, the same command starts a small live calculation instead.

**Step 5: View the figures.** In File Explorer, open `output`, then the newly
created `paper_<timestamp>` folder, then `figures`. Double-click a PDF or
PNG to view it. There are **13 PDFs and 13 PNGs**.

### Subsequent use

Open Command Prompt in the same package folder again, then enter:

```console
.venv\Scripts\activate
python main.py
```

Activate `.venv` again whenever you open a new Command Prompt window.

## 4. Run settings

### Change what the program does

In that Command Prompt window, enter:

```console
notepad settings.py
```

In Notepad, edit and save the existing setting lines as described below.
Back in Command Prompt, enter `python main.py` again to use the new settings.
You do not need to modify `main.py` to choose a mode.

- `MODE` answers **what to do**: redraw, demonstrate, or recompute.
- `SUITE` answers **which datasets to fit**.
- `FIT_PIPELINE` answers **which fitting workflow to use** on those datasets.
- `TASK_RANGE` answers **which numbered tasks to run** in a recomputation.

### Choose a mode: what do you want to obtain?

**A. View the manuscript figures: `MODE = "paper"` (default)**

```python
MODE = "paper"
```

Reads `data/paper/` and redraws all 13 figures as **13 PDFs and 13 PNGs**
under `output/paper_<timestamp>/figures/`. There is no DE/TRF computation.
In this mode, `SUITE`, `FIT_PIPELINE`, `TASK_RANGE`, and the CPU settings do not change the
figures. To redraw only Figures 4, 6 and 7, enter this command in Command Prompt
(with `.venv` active), instead of the plain `python main.py` command:

```console
python main.py --mode paper --figures 4 6 7
```

**B. Try a small real calculation: `MODE = "demo"`**

For a first trial, change the corresponding lines in `settings.py` to:

```python
MODE = "demo"
SUITE = "baseline"
FIT_PIPELINE = "paper"
WORKERS = 1
CORES_PER_FIT = 1
```

Save the file, return to Command Prompt, and run `python main.py` again.

This selects two datasets from the basic experiments—one noiseless and one noisy—and fits both with ER and
RN: **four model fits**. Observation points and fitting ranges are retained,
but DE/TRF budgets are deliberately small.

When final plotting is reached, the output includes a best-fit overview,
`new_run_summary.pdf/.png`, and up to two `demo_timeseries_*.pdf/.png` figures
showing the x(t) and y(t) observations and fitted curves. Each time-series
figure requires a usable fit for that dataset. This is not a reduced version
of all 13 manuscript figures; fits may not have converged and cannot support
the paper's statistical conclusions. Selecting `SUITE = "all"` instead tries
two datasets from each of the three suites, giving 12 model fits in total.
Because full observation times are retained, especially for the highly eccentric
S301 orbits, an `all` demo can still take tens of minutes. Try `baseline` first.

`DEMO_FINISHED_WITH_ITERATION_LIMITS` means the small demonstration budget was
used up, not that the fits converged. An error or `FAILED` is different and
must not be treated as a successful demonstration.

For an even smaller trial—just one dataset fitted by ER and RN—enter this
command in Command Prompt instead:

```console
python main.py --mode demo --suite baseline --max-tasks 2
```

**C. Produce new full-budget results: `MODE = "recompute"`**

For all experiments, change these lines in `settings.py`. This example uses
up to eight CPUs; choose a smaller number if your computer has fewer available:

```python
MODE = "recompute"
SUITE = "all"
FIT_PIPELINE = "paper"
WORKERS = 8
CORES_PER_FIT = 1
TASK_RANGE = None
```

With `TASK_RANGE = None`, this performs **15,660 model fits** and recalculates Figures 1–5 from their
equations. Figures 6–13 use new fits only and are generated when each figure's
required paired results are complete and suitable for plotting. With all required results available,
the output contains **13 manuscript-layout figures plus one summary figure**,
each as PDF and PNG.
If a required grid is incomplete, final plotting still produces the summary,
but not that grid's manuscript figure.

This is a substantial computation, not a quick desktop demonstration.
More CPUs do not guarantee a completion time or identical fitted minima.
Before starting, enter this in Command Prompt to inspect the plan without
performing any fits:

```console
python main.py --mode recompute --dry-run
```

If the plan is what you want, enter `python main.py` to start the computation.

### Choose the datasets: `SUITE`

Used in `demo` and `recompute`, not saved-figure redrawing.
These counts and figure numbers refer to **full recomputation**:

- `"baseline"`: basic experiments before Section 6. **12,960 model fits**;
  supplies Figures **6–11**.
- `"extreme"`: strong-field, high-charge experiments before Section 6.
  **2,592 model fits**; supplies Figure **12**.
- `"s301"`: Section 6 S301-like experiments. **108 model fits**: six noiseless
  ER/RN pairs and 48 pairs with **30-microarcsecond** noise.
  The noisy pairs supply Figure **13**.
- `"all"` (default): all three suites, **15,660 model fits**.
  A full run also computes Figures **1–5**.

One dataset is fitted by both ER and RN, so one pair counts as two model fits.
Changing `SUITE` changes the observations, amount of work, and available
statistical figures. It does not itself force a fitter version.

### Choose the fitting workflow: `FIT_PIPELINE`

Used in `demo` and `recompute`, not saved-figure redrawing in `paper` mode.
It selects a numerical workflow, not ER versus RN;
both theories are still fitted.

- `"paper"` (default): automatically uses **legacy** for `baseline` and
  `extreme`, and **s301** for `s301`, following the manuscript's choices.
- `"legacy"`: uses the old workflow for every selected suite. ER/RN are
  processed as a pair, passing candidate solutions between the two models.
- `"s301"`: uses the newer, two-source S301 workflow for every selected suite.
  ER and RN fits are scheduled independently.

For example, `SUITE = "baseline"` with `FIT_PIPELINE = "s301"` fits the basic
experiment's observations using the newer workflow.

Neither workflow is established as universally better. The first was used for
the earlier experiments; the second was developed for highly eccentric
S301-like orbits. Dataset integration settings are retained; seed and numerical
check policies are in the experiment notes. Fixed seeds do not guarantee
bit-for-bit agreement across environments.

### Choose CPU use: `WORKERS` and `CORES_PER_FIT`

- `WORKERS`: the **total CPU budget**, not the number of experiment groups.
  Use a positive integer within your available CPUs; the current value is in `settings.py`.
- `CORES_PER_FIT`: CPUs assigned to work **inside one fit**. Normally keep
  the default `1`, leaving the CPU budget for parallel fits.

For example, `WORKERS = 8` and `CORES_PER_FIT = 1` allow up to eight work
units at once. A work unit is one task assigned by the launcher; the two
workflows group their tasks differently:

- **Legacy: the ER and RN fits to one dataset form a single task.**
  For example, both fits to dataset A belong to the same paired workflow.
  Its stages run in their prescribed order and may pass candidate solutions
  between models. The pair occupies one single-CPU slot; ER and RN do not
  each receive a CPU and run simultaneously within that pair.
  Eight slots therefore allow **up to eight dataset-pair workflows at once**,
  not 16 simultaneous model fits. A slot takes the next waiting dataset only
  after its entire paired workflow finishes.

- **S301: one model fit to one dataset is a separate task.**
  For example, the ER fit to dataset A and the RN fit to dataset A are two
  independent tasks, each occupying one slot. Eight slots allow **up to
  eight model fits at once**. They may come from different datasets and need
  not remain grouped into four ER/RN pairs. When one fit finishes, its slot
  can take the next task without waiting for the other model of that dataset.

If every selected fit uses the S301 workflow, `WORKERS = 8` and
`CORES_PER_FIT = 2` instead allow up to four simultaneous fits, with two CPUs
inside each. This is not necessarily faster.
**Any use of `legacy` requires `CORES_PER_FIT = 1`**, including the
default automatic `all` run. CPU settings change resource allocation, not
the observations, noise levels, or total number of fits.

### Split a recomputation into numbered ranges: `TASK_RANGE`

`TASK_RANGE = None` (default) selects all tasks. To select tasks 1 through 50,
change the existing setting in `settings.py` to:

```python
TASK_RANGE = (1, 50)
```

Both endpoints are included and numbering starts at **1**. This setting and
the equivalent command option `--task-range 1 50` apply only to `recompute`;
`paper` and `demo` ignore them. A range cannot be combined with
`--task-index` or `--max-tasks`.

The numbered tasks are the work units described above: **one complete ER/RN
pair for legacy, or one model fit for S301**. With the same code, `SUITE`,
`FIT_PIPELINE`, and model selection and order, their order is fixed. The
default models are ER and RN; the advanced `--models` option changes the
selection. Changing the models or their order can change task numbering.
Keep these choices and all experiment settings identical across nodes.

With both ER and RN selected (the default), the full task counts are:

The `paper`, `legacy`, and `s301` columns give the small-task counts for the
corresponding `FIT_PIPELINE` choices. The last column, `model fits`, is the
number of datasets multiplied by two, because each dataset is fitted with
both ER and RN. It is not necessarily twice the small-task count:

```text
SUITE       paper   legacy     s301   model fits
baseline    6,480    6,480    12,960       12,960
extreme     1,296    1,296     2,592        2,592
s301          108       54       108          108
all         7,884    7,830    15,660       15,660
```

For `SUITE="all"` and `FIT_PIPELINE="paper"`, tasks **1–6,480** are
`baseline`, **6,481–7,776** are `extreme`, and **7,777–7,884** are `s301`.
Selecting another suite or workflow creates its own numbering from 1.
These counts describe the work units of the different workflows; their
numerical differences are explained in the workflow section above.

Before assigning ranges, preview a selection without integrating or fitting:

```console
python main.py --mode recompute --suite all --fit-pipeline paper --task-range 1 50 --dry-run
```

The preview reports the full work-unit count, selected range, and selected
model-fit count. For this example, the full plan has **7,884 tasks**,
and tasks **1–50** contain **50 paired tasks / 100 model fits**.

### Run separate ranges on allocated computing nodes, then collect results

Obtain the computing nodes through your computing centre's normal process,
prepare the Python environment, and run the commands below in the package
folder **on the allocated nodes**. These settings only limit CPU use within
the program; they do not request nodes, submit jobs, or schedule across nodes.

For a node with 50 allocated CPU cores, use `--workers 50 --cores-per-fit 1`.
Assign tasks 1–50 to the first node, 51–100 to the second, and continue with
non-overlapping ranges until the plan is covered. Use the same code and
settings everywhere; only the range, available core count, and output directory
should differ. On a shared code directory, use command options as below:
**do not edit the shared `settings.py` while runs are in progress**.

On the first node, run:

```console
python main.py --mode recompute --suite all --fit-pipeline paper --task-range 1 50 --workers 50 --cores-per-fit 1 --output output/node01
```

On the second node, run:

```console
python main.py --mode recompute --suite all --fit-pipeline paper --task-range 51 100 --workers 50 --cores-per-fit 1 --output output/node02
```

Give every range its own output directory. The last range may be shorter
(for example, 7,851–7,884 in the default `all` plan). A 50-task range allows
up to 50 simultaneous tasks, but differing task durations and fewer remaining
tasks can leave cores idle. It does not guarantee that all 50 cores stay busy.

After the runs finish, make their output directories accessible from one
machine, using shared storage or copying them there, then collect them:

```console
python main.py --collect output/node01 output/node02 --output output/combined
```

List all the node output directories you want to combine, not just the two
shown here. Collect results made with the same code, suite, fitting pipeline,
model selection and order, and experiment settings. Collection rejects conflicting
duplicate results; it does not choose whichever has the lowest chi-squared.
Missing results are never filled with saved manuscript fits.

Individual range runs do not calculate the geometry Figures **1–5**.
When collection has the complete, plottable results for `SUITE="all"`,
it calculates those geometry figures once. Figures **6–13** are generated
only when each figure's required result grid is complete and plottable.
Combining only tasks 1–100 therefore does not produce all manuscript figures.

### Find the outputs: `OUTPUT_ROOT`

The default `OUTPUT_ROOT = "output"` creates a new timestamped directory
inside the package for each run, such as `output/demo_<timestamp>/`.
Changing it changes only the save location. Previous runs and supplied
data are not overwritten.

- Every mode: `figures/` holds generated images; `run.json` records the run.
- Fitting modes: `tasks/` holds task inputs and results; `progress.json`
  reports progress. After final plotting, `plot_status.json` records the
  generated manuscript figures. An interrupted run may
  retain results without reaching final plotting.

You can also override settings for a single run by adding options to the
command. Without `--task-range`, recomputation uses the saved `TASK_RANGE`.
To run the full plan in the following example, first restore
`TASK_RANGE = None` in `settings.py` and save it. Entering this command in
Command Prompt then **starts** a full eight-CPU computation:

```console
python main.py --mode recompute --suite all --fit-pipeline paper --workers 8 --cores-per-fit 1
```

See `python main.py --help` for advanced options. `--resume PATH` requires the
same task plan and reuses finished tasks. It refuses saved unfinished tasks
rather than skipping them or automatically restarting an interrupted optimiser.

### If something goes wrong

- **Cannot find `main.py` or `requirements.txt`:** open the folder containing
  those files, then open Command Prompt from its address bar again.
- **A required library is missing:** activate `.venv` in this window and
  repeat `python -m pip install -r requirements.txt`.
- **Only want to see images:** keep `MODE = "paper"`. Do not select
  `recompute` unless you intend to perform the large calculation.
- **Need to stop a fit:** press Ctrl+C once in Command Prompt and wait for
  the program to stop. Existing files remain, but an interrupted optimiser
  is not automatically resumable.

For macOS/Linux, open a terminal in the extracted package folder. With
Python 3.12 installed, use `python3.12 -m venv .venv` and
`source .venv/bin/activate` for environment setup, then the same `python`
commands above. Edit `settings.py` in any plain-text editor rather than Notepad.
See the [Python environment documentation](https://docs.python.org/3.12/library/venv.html).
