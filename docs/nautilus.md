# Optional: run the Nautilus sampler yourself

Notebook 02, section 6, compares a Nautilus posterior (symbolic GCsp likelihood, free
parameters Omega_m, sigma_8, h, nuisance parameters fixed) with the matching
*conditional* Fisher matrix. The repository already contains one finished run
(`data/nautilus/gcsp/`, about 7 minutes on 8 workers), so you do not need to run anything.
To produce your own, the driver script lives in the cosmicfishpie repository (it is not
installed by pip):

```bash
git clone https://github.com/santiagocasas/cosmicfishpie.git
cd cosmicfishpie && git checkout 012e4b8da27a73a574e0f59fc37a5c3e844b5c21

# use the bayes-cfp environment (see README)
OMP_NUM_THREADS=1 python scripts/likelihood/nautilus/run_wl_gcsp_nautilus_case.py \
    --case gcsp --free-params Omegam,sigma8,h \
    --n-live 300 --n-eff 1000 --workers 8 \
    --run-dir ~/bayes_cosmo_nautilus --quiet-sampler
```

Raise `--workers` up to the number of cores you have. Then point the notebook at the result:

```bash
export LECTURE_NAUTILUS_DIR=~/bayes_cosmo_nautilus/gcsp
jupyter lab notebooks/02_cosmicfishpie_live_demo.ipynb
```

Notes:
- The driver script's own docstring shows an outdated path; use the command above.
- Nautilus is an importance nested sampler, not a Metropolis chain; its weighted ESS is
  not an autocorrelation-based ESS.
- In the bundled run, Omega_m and h agree with the conditional Fisher matrix, but sigma_8 does
  not (the sampled posterior is wider and more correlated). We have not resolved
  the origin of this; it is left as an open point in the notebook.
