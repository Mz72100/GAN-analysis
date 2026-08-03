# Generative Modelling Case Study

One notebook covering all four tasks:

- Part 1: a GAN written from scratch on synthetic 2D data (sine baseline, an
  eight-mode Gaussian ring, baseline versus modified generator).
- Part 2.1: a DCGAN on OCTMNIST retinal OCT images.
- Part 2.2: a tabular GAN on CICIDS 2017 (Wednesday BENIGN versus DoS).
- Part 2.3: a DCGAN on QuickDraw birthday-cake sketches.

Entry point: `notebooks/Zubair_Generative_Modelling_Case_Study.ipynb`.

## Layout

```
notebooks/   the notebook (the only entry point)
data/        raw downloads, created on first run and not part of the evidence
Outputs/
  figures/     every figure the report uses
  metrics/     loss histories, per-task metric JSON, run manifest, self-check
  checkpoints/ generator checkpoints and the fitted CICIDS scaler
```

`data/` and `Outputs/` are created by the run. Both paths are project-relative;
set the `OUTPUT_DIR` or `DATA_DIR` environment variables to move either one.

## Setup

Colab (used for the recorded run): open the notebook, set Runtime -> Change
runtime type -> GPU. The notebook installs `medmnist` and the FID backend itself;
nothing else is needed.

Local:

```bash
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
```

Install `torch` from the PyTorch index matching your CUDA build if you want GPU
training. On CPU the full run is impractically slow; use the smoke test instead.

## Data

All three datasets download automatically on first run into `data/`:

- OCTMNIST via the `medmnist` package (MedMNIST v2). Roughly 55 MB.
- CICIDS 2017 `Wednesday-workingHours.pcap_ISCX.csv` from a public mirror, about
  225 MB. The notebook records the size and SHA-256 of the file it actually used
  in `Outputs/metrics/cicids_source.json`. If the download fails it falls back to
  a clearly labelled synthetic stand-in, and the self-check at the end reports
  that the real capture was not used.
- QuickDraw `birthday cake` numpy_bitmap from the Google bucket, about 100 MB.

Nothing needs to be uploaded or mounted by hand.

## Running

Smoke test (wiring only, a few minutes):

1. Open the notebook, set `QUICK_MODE = True` in the setup cell.
2. Run all cells.
3. Confirm the final self-check table prints and does not raise.

A smoke run is not a result. Its epoch counts are cut to a handful and its numbers
must not be reported.

Full run (the one that produces the submitted figures and numbers):

1. Set `QUICK_MODE = False` in the setup cell (this is the default).
2. Runtime -> Run all, on a GPU runtime.
3. Run the notebook top to bottom in a single session and do not clear the
   outputs afterwards. The saved outputs and their execution counts are part of
   the evidence that the notebook ran in order.
4. Wait for the final cell. It prints a requirement-by-requirement PASS/FAIL table
   and raises if any required figure, metric or checkpoint is missing or if any
   reported metric is not finite.

## Outputs

Every figure, metric file and checkpoint the report depends on is written under
`Outputs/`, and the final cell also zips that tree to `Outputs_bundle.zip` next to
`Outputs/`. On Colab it additionally offers the zip as a download, because the
Colab filesystem does not persist. Download it and place the unzipped `Outputs/`
tree in this folder so the submitted code and its evidence travel together.

Key files:

- `Outputs/metrics/run_manifest.json` - device, seeds, resolved package versions
  and every metric this run computed, in one file.
- `Outputs/metrics/requirements_selfcheck.json` - the PASS/FAIL table.
- `Outputs/metrics/*_losses.csv` - full per-epoch loss histories.
- `Outputs/checkpoints/*.pt` - the generators the evaluation cells reload.

## Reproducibility notes

Seeds are fixed for Python, NumPy and PyTorch (`SEED = 42`) and re-applied before
each training call. GPU kernel scheduling is still nondeterministic, so exact
metric values will move slightly between runs on the same hardware and more so on
different hardware. Every number quoted in the report must be taken from the
`Outputs/` tree produced by the run being submitted, not from an earlier run.
