# Bayesian inference in cosmology: slides and notebooks

Two one-hour lectures by Santiago Casas (RWTH Aachen, October 2026): from Bayes' theorem
to Fisher forecasts and MCMC for Euclid-era large-scale-structure analyses.

## Contents

| Path | What |
|---|---|
| `slides/index.html` | The slides (reveal.js, self-contained, works offline). Open in a browser. |
| `slides/slides_bayes-cosmo.pdf` | The same slides as a PDF. |
| `notebooks/01_bayes_toy_example.ipynb` | Hour 1. Hubble-law toy model: updating, chi^2, Fisher, Metropolis-Hastings from scratch, evidence. Only numpy/scipy/matplotlib. |
| `notebooks/02_cosmicfishpie_live_demo.ipynb` | Hour 2. Euclid-like Fisher forecasts with `cosmicfishpie`, marginal vs conditional Fisher, comparison with a MontePython chain and a Nautilus run. |
| `notebooks/03_cloelike_bao_demo.ipynb` | Hour 2. A BAO likelihood with `cloelib` / `cloelike`, evaluated on a grid of H0. |
| `data/` | Everything the notebooks read, so no download is needed (see `data/manifest.json` for provenance and checksums). |
| `envs/` | Environment files for mamba/conda and for uv. |
| `docs/nautilus.md` | Optional: how to re-run the Nautilus sampler. |

Notebook outputs are included, so you can read them without running anything.

## Setup

Two environments are needed, because `cosmicfishpie` requires `numpy<2` and `cloelib` requires
`numpy>2`. Both use Python 3.12.

| Notebook | Environment |
|---|---|
| 01, 02 | `bayes-cfp` |
| 03 | `bayes-cloe` |

### With mamba (or conda)

```bash
mamba env create -f envs/environment-cfp.yml     # notebooks 01, 02
mamba env create -f envs/environment-cloe.yml    # notebook 03
mamba activate bayes-cfp
jupyter lab
```

### With uv

```bash
uv venv --python 3.12 .venv-cfp
uv pip install --python .venv-cfp/bin/python -r envs/requirements-cfp.txt
uv venv --python 3.12 .venv-cloe
uv pip install --python .venv-cloe/bin/python -r envs/requirements-cloe.txt

.venv-cfp/bin/jupyter lab      # then open notebook 01 or 02
.venv-cloe/bin/jupyter lab     # then open notebook 03
```

The first install takes a few minutes (`camb`, `classy`, `jax` and friends). `cosmicfishpie` is installed
from GitHub (it is not on PyPI), pinned to a commit.

Run the notebooks from inside this repository (they find the `data/` folder by walking up
from the current directory). Generated files go to `output/`, which is git-ignored.

## Timings

Measured on a laptop, fresh environments built from the files in `envs/`:

| Step | Time |
|---|---|
| Notebook 01, all cells | about 25 s |
| Notebook 02, all cells (symbolic backend, `ell_sampling=25`) | about 30 s; the three Fisher runs take 8.6 s, 7.7 s and 3.1 s |
| Notebook 03, all cells | about 11 s |

## Caveats

- **Symbolic power spectra.** The live demo uses the fast `symbolic` backend of `cosmicfishpie`. It is an
  approximation: in stored comparisons it differs from CLASS by about 1-14% in photometric errors (32% on
  `n_s`) and 10-33% in spectroscopic errors. CAMB and CLASS agree to better than 1%.
- **Baseline Fisher matrices in `data/baseline/` are modern reruns** (CAMB/CLASS, high precision, Paper
  pessimistic specs, 2303.09451 fiducial), not the matrices published in the paper. Photometric errors agree with
  the paper's Table 8. The spectroscopic reruns are 30-40% larger than the paper's CosmicFish values
  (nuisance-parameter setup differs); the notebook compares only the photometric case.
- **MontePython chains** (`data/baseline/montepython_samples.npz`) are archived chains of the photometric
  pessimistic `w0waCDM` case, with the first 30% of each chain removed. Their marginal errors agree with the
  paper's MP/MCMC column within 5%. Convergence (R-hat, ESS) was not re-checked.
- **Neutrino case.** The archived MontePython chain for LCDM+Mnu+Neff frees 9 cosmological parameters, so it
  cannot be overlaid on the 7-parameter Fisher matrix. The slides and notebook use the published figure
  (Euclid Collab., arXiv:2405.06047, Fig. 10) instead.
- **Nautilus vs Fisher.** Omega_m and h agree; sigma_8 does not (see `docs/nautilus.md`). This is an open point.
- **Paper typos worth knowing:** Table 10 of 2303.09451 (MP/MCMC spectroscopic pessimistic column) repeats the
  optimistic values; 2405.06047 quotes both 56 meV (abstract, Table 5) and 52 meV (conclusions) for Euclid-alone
  sigma(Sum m_nu); Table 1 of Verde (0911.3105) lists 90% values in its 95.4% row.
- `cloelike` has no built-in Cobaya or MontePython wrapper; the full CAMB path is not differentiable.

## Credits and references

- Padilla et al., arXiv:1903.11127; Verde, arXiv:0911.3105; Heavens, arXiv:0906.0664.
- Casas et al., Euclid: Validation of the MontePython forecasting tools, arXiv:2303.09451.
- Euclid Collaboration, Sensitivity to neutrino parameters, arXiv:2405.06047.
- Figures in `slides/` taken from these papers are credited on the slides; the Bayes' theorem visual proof is from
  Wikimedia Commons (CC BY-SA).
