# sAFP Raman Algorithm

Research-oriented Raman spectroscopy analysis resources for **sAFP-related analysis** and an accompanying browser-based **HCC Risk & Decision Aid**.

The repository currently contains:

- a standalone HTML risk calculator based on age and Raman peak-intensity ratios;
- three packaged analysis archives;
- research code and supporting resources distributed for reproducibility and further development.

> **Research use only.** This repository is not a medical device and must not be used as the sole basis for diagnosis, screening, treatment, or other clinical decisions.

---

## Repository Contents

| File | Description |
|---|---|
| `index.html` | Standalone HCC risk and decision-aid webpage. Runs locally in a modern browser without a server. |
| `sAFP-CLA.rar` | Packaged sAFP analysis module. Extract the archive before use. |
| `sAFP-IPA.rar` | Packaged sAFP analysis module. Extract the archive before use. |
| `sAFP-RAA.rar` | Packaged sAFP analysis module. Extract the archive before use. |

The `.rar` archives are distributed as packaged modules. Their exact entry points, data files, and environment requirements should be checked after extraction.

---

## HCC Risk & Decision Aid

The `index.html` page calculates an estimated probability using:

- age;
- Raman peak intensity at **500 cm⁻¹**;
- Raman peak intensity at **650 cm⁻¹**;
- Raman peak intensity at **720 cm⁻¹**;
- Raman peak intensity at **1035 cm⁻¹**.

The page automatically derives the following ratios:

- `650 / 500`;
- `720 / 500`;
- `1035 / 500`.

It then applies a logistic model with optional uniform shrinkage and prevalence recalibration.

### Model Formula

\[
p = \operatorname{logistic}
\left(
\beta_0
+
s\left[
\beta_1 \cdot \text{Age}
+
\beta_2 \cdot \frac{I_{720}}{I_{500}}
+
\beta_3 \cdot \frac{I_{1035}}{I_{500}}
+
\beta_4 \cdot \frac{I_{650}}{I_{500}}
\right]
+
\Delta
\right)
\]

where:

\[
\Delta =
\operatorname{logit}(\pi_{\text{target}})
-
\operatorname{logit}(\pi_{\text{dev}})
\]

and:

- \(p\) is the predicted probability;
- \(s\) is the uniform shrinkage factor;
- \(\pi_{\text{dev}}\) is the development-dataset prevalence;
- \(\pi_{\text{target}}\) is the target-population prevalence.

### Default Coefficients

| Parameter | Default value |
|---|---:|
| Intercept, \(\beta_0\) | `-6.7180528703` |
| Age, \(\beta_1\) | `0.1085953017` |
| `720/500`, \(\beta_2\) | `0.8924109223` |
| `1035/500`, \(\beta_3\) | `0.7350571922` |
| `650/500`, \(\beta_4\) | `0.4331492809` |
| Uniform shrinkage, \(s\) | `1.00` |
| Development prevalence | `0.61` |

All coefficients and recalibration settings can be edited directly on the webpage.

---

## Quick Start

### 1. Download the repository

```bash
git clone https://github.com/Xyu818/sAFP-Raman-Algorithm.git
cd sAFP-Raman-Algorithm
```

Alternatively, download the repository as a ZIP file from GitHub.

### 2. Open the browser tool

Open `index.html` directly in a modern browser.

No backend service, package installation, or build step is required for the HTML calculator.

For a local HTTP server, run one of the following commands:

```bash
python -m http.server 8000
```

Then visit:

```text
http://localhost:8000
```

### 3. Enter the inputs

1. Enter age.
2. Enter the raw peak intensities.
3. Select the intended scenario.
4. Review the calculated ratios, predicted probability, selected threshold, and recommendation.
5. Adjust coefficients, shrinkage, or prevalence settings only when scientifically justified.

---

## Built-in Scenarios

The webpage provides two preset scenarios:

| Scenario | Default decision threshold | Default target prevalence |
|---|---:|---:|
| HBV-cirrhosis surveillance clinic | `0.05` | `0.03` |
| General check-up population | `0.005` | `0.0002` |

These values are editable and should be replaced with locally validated values when appropriate.

---

## Input Notes

- The intensity at **500 cm⁻¹ must be greater than zero** because it is used as the denominator.
- Peak intensities should be non-negative.
- The 650 cm⁻¹ input is optional in the current interface.
- When the 650 cm⁻¹ value is absent, its model contribution is treated as zero. This may underestimate the calculated risk.
- Raman spectra should be processed consistently before peak intensities are extracted.
- Do not mix spectra obtained using incompatible acquisition settings, preprocessing pipelines, units, or normalization procedures.

---

## Browser Storage and Privacy

The webpage is implemented with HTML, CSS, and JavaScript and performs calculations locally in the browser.

- No backend server is required.
- The current page does not include code that uploads inputs to a remote service.
- Clicking **Save settings** stores entered values and model settings in the browser's `localStorage`.
- Clicking **Reset** removes the saved values and restores the defaults.

When working with sensitive data, use a controlled device and clear browser storage after use.

---

## Packaged Modules

Extract each archive into a separate directory:

```text
sAFP-CLA/
sAFP-IPA/
sAFP-RAA/
```

Because the modules are distributed as `.rar` archives, extraction software with RAR support is required.

Examples include:

- 7-Zip;
- WinRAR;
- `unrar`.

After extraction, inspect each module for its own scripts, data templates, models, and documentation before execution.

---

## Possible Python Dependencies

Some historical analysis scripts associated with this project use common scientific Python packages such as:

```text
numpy
pandas
scipy
scikit-learn
matplotlib
joblib
openpyxl
xgboost
lightgbm
seaborn
```

The exact dependency set may differ between packaged modules. A dedicated virtual environment is recommended:

```bash
python -m venv .venv
```

Activate it on Windows:

```bash
.venv\Scripts\activate
```

Activate it on macOS or Linux:

```bash
source .venv/bin/activate
```

Install only the packages required by the extracted module.

---

## Recommended Data Organization

A consistent project structure helps preserve reproducibility:

```text
project/
├── data/
│   ├── raw/
│   └── processed/
├── models/
├── results/
├── scripts/
└── README.md
```

Do not commit identifiable clinical data, private patient information, access credentials, or confidential datasets to a public repository.

---

## Reproducibility Recommendations

For reproducible analysis, record:

- Raman instrument and laser wavelength;
- laser power and acquisition time;
- spectral range and spectral resolution;
- sample preparation procedure;
- baseline-correction method;
- smoothing and normalization settings;
- peak-extraction method;
- training and test split;
- random seed;
- software and package versions;
- model-selection and validation strategy.

Model performance should be evaluated using independent data whenever possible. Internal validation alone is not sufficient to establish clinical utility.

---

## Limitations

- The risk calculator implements a predefined mathematical model; it does not retrain the model.
- Editable coefficients can produce invalid or clinically inappropriate outputs when changed without justification.
- Prevalence recalibration changes the estimated probability and should be based on an appropriate target population.
- Threshold-based recommendations are decision aids only.
- External validation, calibration assessment, and prospective evaluation are required before clinical application.
- The repository currently does not provide a formal software release, automated tests, or a documented package API.

---

## Citation

This repository accompanies research published in *Small Methods*. The DOI has not yet been assigned.

Please cite the associated article using the following provisional format:

```text
Author(s). Article title. Small Methods. 2026. DOI: pending.
```

The complete article title, author list, volume, issue, article number, and DOI should be added once the final bibliographic information becomes available.

Suggested provisional BibTeX entry:

```bibtex
@article{safp_raman_2026,
  title   = {Article title},
  author  = {Author names},
  journal = {Small Methods},
  year    = {2026},
  note    = {Published; DOI pending}
}
```

## License

No open-source license is currently included in this repository.

Unless a license is added, reuse, redistribution, and modification rights are not automatically granted. Contact the repository owner for permission before reusing the code or packaged resources.

---

## Contact

For questions about the data, model, Raman preprocessing workflow, or packaged modules, contact the repository owner through GitHub:

**Xyu818**
