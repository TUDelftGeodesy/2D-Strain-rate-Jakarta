# Jakarta_strain_rate

A Python implementation to estimate horizontal strain-rate tensors and derived strain-rate quantities from InSAR-based regional uniform-motion (RUM) horizontal velocities, applied over Jakarta. The method uses area-average RUM velocity observations, Q8 quadratic quadrilateral finite elements, regularized inversion, and first-order covariance propagation.

This repository provides the implementation of the horizontal strain-rate inversion method introduced in the accompanying manuscript. It also enables the reconstruction of the Jakarta strain-rate results and figures supporting the manuscript.

**Associated manuscript:** Lapadat and Hanssen, *Horizontal Strain Rate Estimation from InSAR Observations and the Strapdown Method*. Manuscript submitted to *Journal of Geophysical Research: Solid Earth*.

## Repository contents

The workflow is divided into two notebooks:

* `01_jakarta_strain_inversion.ipynb` implements the strain-rate inversion method. It either performs the inversion or loads the precomputed inversion results, derives the strain-rate quantities and their within-element covariance blocks, and exports the numerical results;
* `02_jakarta_strain_figures.ipynb` loads these results and reconstructs the strain-rate, uncertainty, and velocity-residual figures from the Results section of the manuscript.

**Note:** Running the complete inversion is computationally expensive. The first notebook can instead load `results/jakarta_strain_inversion_archive.pkl`, allowing users to reproduce the result tables and manuscript figures without repeating the inversion.

## Requirements

Python 3.12 is required. Conda installs it automatically. This project depends on the following Python packages:

* `numpy`
* `scipy`
* `pandas`
* `matplotlib`
* `contextily`
* `shapely`
* `pyproj`
* `pillow`
* `requests`
* `jupyterlab`
* `ipykernel`

The tested package versions are specified in `pyproject.toml`. Conda must be installed before continuing.

From the repository directory, create the isolated `jakarta-strain` environment, install the project and its dependencies from `pyproject.toml`, and start JupyterLab.

On macOS or Linux:

```bash
conda create -n jakarta-strain python=3.12 pip -y
conda run -n jakarta-strain python -m pip install -e .
conda run -n jakarta-strain jupyter lab
```

On Windows Command Prompt or PowerShell:

```powershell
conda create -n jakarta-strain python=3.12 pip -y
conda run -n jakarta-strain python -m pip install -e .
conda run -n jakarta-strain jupyter lab
```

The first command creates the environment, the second installs the repository and its dependencies, and the third starts JupyterLab. For later sessions, only the third command is required:

```bash
conda run -n jakarta-strain jupyter lab
```

Both notebooks use the standard `python3` kernel supplied by the `jakarta-strain` environment. No environment activation or manual kernel registration is required.

## Data and automatic preparation

The GitHub repository contains the notebooks and code, but not the data or precomputed results. The first code cell of `01_jakarta_strain_inversion.ipynb` prepares them automatically. It creates the `data/`, `results/`, and `figures/` directories, keeps existing files unchanged, and downloads and validates any missing files.

The files come from two 4TU.ResearchData datasets:

* **RUM velocity observations:** the separately published Jakarta RUM velocity dataset [![DOI](https://img.shields.io/badge/DOI-10.4121%2F39e7a39c--d305--425f--b238--1f3a4271dda9.v3-F29100.svg)](https://doi.org/10.4121/39e7a39c-d305-425f-b238-1f3a4271dda9.v3). Its file `jakarta_enu_estimates.csv` contains RUM East and North velocity estimates, with one 2 × 2 East–North variance-covariance block per RUM;
* **Jakarta strain-rate dataset:** the accompanying dataset [![DOI](https://img.shields.io/badge/DOI-10.4121%2Fcff30595--1d98--4e9d--8eb7--0a3000a434fe-F29100.svg)](https://doi.org/10.4121/cff30595-1d98-4e9d-8eb7-0a3000a434fe) contains the transversal horizontal-velocity contours, precomputed inversion results, strain-rate result tables, and metadata.

### File roles

The `data/` directory contains the two input files used by the workflow:

* `data/jakarta_enu_estimates.csv` contains the RUM velocity observations used by the inversion and the velocity-residual figures;
* `data/jakarta_transv_horiz_vel_contours.npz` contains transversal horizontal-velocity contours used only as contextual information in the maps.

The `results/` directory stores the inversion archive and strain-rate results. Missing files are downloaded from 4TU.ResearchData and can be recreated by the first notebook.

* `results/jakarta_strain_inversion_archive.pkl` contains the inversion results. It is loaded when `RUN_FULL_INVERSION = False` and recreated when `RUN_FULL_INVERSION = True`;
* `results/jakarta_strain_tensor.csv` and `results/jakarta_strain_quantities.csv` contain the strain-rate results produced by the first notebook and used by the second;
* `results/jakarta_strain_metadata.json` documents the two result tables.

The required files depend on how the workflow is used:

* a **complete inversion** requires `data/jakarta_enu_estimates.csv`. Creating the maps also requires `data/jakarta_transv_horiz_vel_contours.npz`;
* the default **archive-assisted workflow** requires `data/jakarta_enu_estimates.csv` and `results/jakarta_strain_inversion_archive.pkl`. Creating the maps also requires `data/jakarta_transv_horiz_vel_contours.npz`;
* a **figures-only workflow** requires both files in `data/`, `results/jakarta_strain_inversion_archive.pkl`, `results/jakarta_strain_tensor.csv`, and `results/jakarta_strain_quantities.csv`.

The preparation cell checks all six files regardless of the selected route, leaving the repository ready for either notebook without manual downloads or file placement.

### Expected folder structure

After the preparation cell has completed, the repository has the following structure:

```text
<repository>/
├── README.md
├── pyproject.toml
├── 01_jakarta_strain_inversion.ipynb
├── 02_jakarta_strain_figures.ipynb
├── data/
│   ├── jakarta_enu_estimates.csv
│   └── jakarta_transv_horiz_vel_contours.npz
├── results/
│   ├── jakarta_strain_tensor.csv
│   ├── jakarta_strain_quantities.csv
│   ├── jakarta_strain_metadata.json
│   └── jakarta_strain_inversion_archive.pkl
└── figures/
```

## Usage

Open `01_jakarta_strain_inversion.ipynb` and run its first code cell to prepare the required files. Then choose one of the following routes.

### Perform the complete inversion

Set:

```python
RUN_FULL_INVERSION = True
```

Run the remaining cells of the first notebook to estimate the nodal velocities, propagate the uncertainties, derive the strain-rate quantities, and export the result files. Then run `02_jakarta_strain_figures.ipynb` to reconstruct the manuscript figures.

### Bypass the inversion using the precomputed inversion archive

Keep the default setting:

```python
RUN_FULL_INVERSION = False
```

The first notebook loads `results/jakarta_strain_inversion_archive.pkl`, derives the strain-rate quantities and covariance blocks, and exports the strain-rate result tables without repeating the expensive inversion. Then run `02_jakarta_strain_figures.ipynb` to reconstruct the manuscript figures.

### Reconstruct the figures only

Open and run `02_jakarta_strain_figures.ipynb`. This route uses the downloaded strain-rate result tables and inversion archive without running the inversion or the remaining cells of the first notebook.

## Output

The workflow produces:

* `results/jakarta_strain_tensor.csv`, containing one area-averaged strain-rate tensor and covariance block per finite element;
* `results/jakarta_strain_quantities.csv`, containing the principal strain rates, first invariant, maximum engineering shear strain rate, and their covariance block per finite element;
* `results/jakarta_strain_metadata.json`, describing the observations, finite-element model, regularization, uncertainty propagation, archive, and output CSV tables;
* `results/jakarta_strain_inversion_archive.pkl`, containing the saved inversion results and the quantities required to bypass the inversion; and
* manuscript figures in the `figures/` directory.

Strain rates are expressed in microstrain/yr and their variances and covariances in (microstrain/yr)^2. A blank `rum_id` indicates that the finite element has no observed RUM.

## License

This project is licensed under the [Apache License 2.0](LICENSE).
