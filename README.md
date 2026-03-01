# SWGO Dark Matter Sensitivity — Dwarf Galaxy Analysis

Jupyter notebooks to compute SWGO 95% C.L. upper limits on the DM annihilation cross-section $\langle\sigma v\rangle$ using dwarf spheroidal galaxies as targets.

> **Note on IRFs:** This code uses the **SWGO strawman IRFs** (an early, idealised detector model), not the most recent SWGO performance estimates. Results should be interpreted accordingly.

> **Origin:** This code was developed as part of my Master's dissertation:  
> *"Dark matter searches with the Southern Wide-field Gamma-ray Observatory"*, University of São Paulo (2023).  
> Full text available at: <https://www.teses.usp.br/teses/disponiveis/76/76134/tde-05102023-105105/en.php>

---

## Physics overview

The expected $\gamma$-ray photon flux from DM pair-annihilation is:

$$
\frac{d\Phi}{dE} = \frac{\langle\sigma v\rangle}{8\pi\, m_{\rm DM}^2}\,\frac{dN}{dE}(E, m_{\rm DM}) \cdot J
$$

where

| Symbol | Meaning | Units |
|--------|---------|-------|
| $\langle\sigma v\rangle$ | velocity-averaged annihilation cross-section | cm³ s⁻¹ |
| $m_{\rm DM}$ | DM particle mass | GeV |
| $dN/dE$ | photon spectrum per annihilation | GeV⁻¹ |
| $J = \int \rho^2\, ds\, d\Omega$ | J-factor (line-of-sight integral) | GeV² cm⁻⁵ |

Upper limits are set with a Poisson log-likelihood ratio (Wilks' theorem), scanning $\langle\sigma v\rangle$ until the test statistic reaches $TS = 2.71$.

---

## Notebooks (run in order)

| Notebook | What it does |
|----------|-------------|
| `SWGO_Gen_Ns.ipynb` | Computes **`gen_ns`** (signal template per unit $\langle\sigma v\rangle \times J$) for a single chosen channel. |
| `Dwarf_Sigma_Calc.ipynb` | Applies J-factors and derives individual and combined $\langle\sigma v\rangle$ upper limits. |
| `Plot.ipynb` | Plots the results. |

---

## `gen_ns` — the key intermediate quantity

`gen_ns` is defined as

$$
\text{gen\_ns} = \frac{N_{\rm signal}}{\langle\sigma v\rangle \cdot J}
$$

It encodes the **SWGO detector response** (effective area, energy dispersion, observation time) convolved with the **DM spectrum** for a given channel.  
Because it does **not** depend on the source, it only needs to be computed once per channel and can then be multiplied by any target's J-factor.

The output is a 2-D array of shape `(n_bkg_bins, n_mass)` stored in `results/gen_ns/<channel>.txt`.

---

## Folder structure

```
.
├── inputs/                        ← SWGO strawman IRFs (from harmscho/SGSOSensitivity)
│   ├── hArea_swgo_i_g.txt         ← Effective area vs energy
│   ├── swgo_BckRate_per_sr.txt  ← Background rate per sr
│   └── SWGO_i_Edisp_g.txt         ← Energy dispersion matrix
│
├── dwarfs/                        ← Dwarf galaxy data (Geringer-Sameth et al. 2015)
│   ├── anglesSWGO.txt             ← Integration angles [deg]
│   ├── table1SWGO.txt             ← Dwarf names and ordering
│   ├── table3complete.txt         ← J-factors and uncertainties
│   └── rmaxSWGO.txt               ← Maximum integration radii
│
├── AtProduction_gammas.dat        ← PPPC4DMID photon spectra
│
├── results/
│   ├── gen_ns/                    ← gen_ns arrays (output of SWGO_Gen_Ns)
│   └── sigma/                     ← σv upper limits (output of Dwarf_Sigma_Calc)
│
├── SWGO_Gen_Ns.ipynb
├── Dwarf_Sigma_Calc.ipynb
└── Plot.ipynb
```

---

## External data sources

| File(s) | Source |
|---------|--------|
| `inputs/*.txt` (SWGO strawman IRFs) | [harmscho/SGSOSensitivity](https://github.com/harmscho/SGSOSensitivity) |
| `Dwarfs/table3complete.txt` (J-factors) | [Geringer-Sameth et al. 2015, ApJ 801, 74](https://iopscience.iop.org/article/10.1088/0004-637X/801/2/74) |
| `AtProduction_gammas.dat` (spectra) | [PPPC4DMID](http://www.marcodelta.com/pppc4dmid/) |

---

## Dependencies

```
numpy
scipy
matplotlib
joblib
```

Install with: `pip install numpy scipy matplotlib joblib`

---

## Parallelisation

Both `SWGO_Gen_Ns` and `Dwarf_Sigma_Calc` use `joblib.Parallel`.  
The number of CPU cores is set by the `N_JOBS` variable in each notebook's **Input Cell** (`-1` = all available cores, `1` = serial for debugging).

---

## Available annihilation channels

The following channel names can be used (from PPPC4DMID):

`eL`, `eR`, `e`, `muL`, `muR`, `mu`, `tauL`, `tauR`, `tau`,  
`q`, `c`, `b`, `t`, `WL`, `WT`, `W`, `ZL`, `ZT`, `Z`,  
`g`, `gamma`, `h`, `nue`, `numu`, `nutau`, `Ve`, `Vmu`, `Vtau`
