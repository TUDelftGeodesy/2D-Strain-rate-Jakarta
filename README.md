# Jakarta_strain_rate

A Python implementation to estimate horizontal strain-rate tensors and derived strain-rate quantities from InSAR-based regional uniform-motion (RUM) horizontal velocities, applied over Jakarta. The method uses area-average RUM velocity observations, Q8 quadratic quadrilateral finite elements, regularized inversion, and first-order covariance propagation.

This repository provides the implementation of the horizontal strain-rate inversion method introduced in the accompanying manuscript. It also enables the reconstruction of the Jakarta strain-rate results and figures supporting the manuscript.

	Lapadat and Hanssen, *Horizontal Strain Rate Estimation from InSAR Observations and the Strapdown Method*,  
manuscript submitted to *Journal of Geophysical Research: Solid Earth*.

## Repository contents

The workflow is divided into two notebooks:

* `01_jakarta_strain_inversion.ipynb` contains the implementation of the strain-rate inversion method proposed in the manuscript. 
It loads the RUM velocities, either performs the inversion from scratch or loads precomputed inversion results, 
derives the strain-rate quantities and their within-element covariance blocks, 
and exports the numerical results;
* `02_jakarta_strain_figures.ipynb` loads the exported results of the first notebook and reconstructs the strain-rate, uncertainty, and velocity-residual figures from the Results section of the manuscript.

Note: running the complete strain-rate inversion in `01_jakarta_strain_inversion.ipynb` is computationally expensive. 
The cell containing the inversion method is therefore commented out by default, and `results/jakarta_strain_inversion_archive.pkl` provides the precomputed Jakarta strain-rate inversion results. 
This allows users to inspect the method, derive the strain-rate quantities, and reproduce the manuscript figures without repeating the expensive inversion.

## Requirements

Python 3.12 is required. This project depends on the following Python packages:

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

The tested package versions are specified in `pyproject.toml`.

From the repository directory, create the isolated `jakarta-strain` environment, install the project and its dependencies, and configure the notebook kernel with one command.

On macOS or Linux:

```bash
python3.12 -m venv jakarta-strain && jakarta-strain/bin/python -m pip install -e . && jakarta-strain/bin/python -m ipykernel install --prefix jakarta-strain --name jakarta-strain --display-name "Python (Jakarta strain)"
```

On Windows Command Prompt:

```bat
py -3.12 -m venv jakarta-strain && jakarta-strain\Scripts\python.exe -m pip install -e . && jakarta-strain\Scripts\python.exe -m ipykernel install --prefix jakarta-strain --name jakarta-strain --display-name "Python (Jakarta strain)"
```

Start JupyterLab from the configured environment:

```bash
jakarta-strain/bin/python -m jupyter lab
```

On Windows, use `jakarta-strain\Scripts\python.exe -m jupyter lab`. Both notebooks are already configured to use the `Python (Jakarta strain)` kernel. No environment activation or user-level kernel installation is required.

## Input Data

To estimate a horizontal strain-rate field, the method requires:

* RUM East and North velocity estimates, with one 2 × 2 East–North variance-covariance block per RUM.

The Jakarta RUM velocity estimates are available through 4TU.ResearchData, [![DOI](https://data.4tu.nl/v3/datasets/22215562/doi-badge.svg)](https://doi.org/10.4121/39e7a39c-d305-425f-b238-1f3a4271dda9.v3). If the public CSV is not already present in the `data` directory, `01_jakarta_strain_inversion.ipynb` downloads and validates it automatically.

The repository also includes `data/jakarta_transv_horiz_vel_contours.npz`, which contains the transversal horizontal-velocity contours used as contextual information in the maps.

## Usage

Run the notebooks in numerical order.

`01_jakarta_strain_inversion.ipynb` has by default `RUN_FULL_INVERSION = False`, and uses the distributed archive with strain-rate inversion results. 
To perform the inversion from the RUM velocities, set `RUN_FULL_INVERSION = True`. 
After the first notebook exports the result tables, run `02_jakarta_strain_figures.ipynb` to reconstruct the manuscript figures.

## Output

The workflow produces:

* `results/jakarta_strain_tensor.csv`, containing one area-averaged strain-rate tensor and covariance block per finite element;
* `results/jakarta_strain_quantities.csv`, containing the principal strain rates, first invariant, maximum engineering shear strain rate, and their covariance block per finite element;
* `results/jakarta_strain_metadata.json`, describing the observations, finite-element model, regularization, uncertainty propagation, archive, and output csv tables;
* manuscript figures in the `figures` directory.

Strain rates are expressed in microstrain/yr and their variances and covariances in (microstrain/yr)^2. A blank `rum_id` indicates that the finite element has no observed RUM.
