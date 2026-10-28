# Predicting Individual Reaction Time from Task-fMRI Functional Connectivity

Machine Learning Course (SBEG381), Biomedical Engineering, Cairo University

Can network-level brain connectivity during a working-memory task predict how fast a person responds? This project uses Elastic Net regression with Recursive Feature Elimination (RFE) on Human Connectome Project (HCP) task-fMRI data to predict individual 2-back reaction time (RT), and focuses on explaining which brain-network connections drive the prediction.

## Highlights

- Reduced 360 Glasser cortical ROIs to 12 functional networks and 78 connectivity features per subject.
- Elastic Net + RFE trained inside nested cross-validation.
- Test-set performance: **r ≈ 0.21, R² = 0.045, MAE ≈ 92.6 ms, RMSE ≈ 111.1 ms**, in line with the range reported for connectome-based predictive modeling of working memory.
- Explainability through a feature-importance chart, a network adjacency heatmap, a 2D connectome, and a 3D cortical surface map.

## Dataset

- Neuromatch Academy (NMA) curated subset of the HCP S1200 task-fMRI release.
- 100 subjects, HCP Working Memory (N-back) task, two runs per subject (LR and RL).
- Data are already preprocessed and parcellated into 360 cortical ROIs (Glasser atlas).
- **The raw data is not included in this repository** because of HCP data-use terms. Please obtain it from the NMA / HCP sources.

## Method

1. **Features:** ROI time series are mean-centered, averaged within 12 functional networks, and correlated pairwise (Pearson). The upper triangle (including the diagonal) gives 78 features per run; the two runs are averaged per subject. Final matrix: X ∈ R^(100×78).
2. **Target:** mean 2-back reaction time per subject, averaged across both runs (y ∈ R^100).
3. **Split and scaling:** 80/20 train/test split; z-score normalization fitted on training data only to avoid leakage.
4. **Model:** Elastic Net (combined L1 and L2 penalties) to handle correlated connectivity features.
5. **Feature selection:** RFE wrapper that repeatedly trains the model, ranks features by absolute coefficient, and prunes the weakest.
6. **Validation:** Nested cross-validation. The inner loop grid-searches α ∈ {0.1, 1, 10}, L1 ratio ∈ {0.1, 0.5, 0.9}, and number of RFE features ∈ {10, 20, 40}.

## Results

| Metric | Value |
|---|---|
| Correlation (r) | ≈ 0.21 |
| R² | 0.0452 |
| MAE | 92.56 ms |
| RMSE | 111.13 ms |

The modest R² is typical for behavior prediction from fMRI connectivity, where signal-to-noise is low and samples are small.

### Key findings

- Strongest facilitator (faster RT): **Language–Visual1** (β = −2.101).
- Strongest inhibitor (slower RT): **Language–Somatomotor** (β = +1.525).
- Other positive contributors: Auditory–Somatomotor (β = +1.368) and Cingulo-Opercular–Orbito-Affective (β = +1.357).
- Prefrontal and posterior cingulate connectivity emerged as the main hotspots in the cortical projection.
- Reaction time appears to reflect a balance between visuolinguistic coupling and somatomotor/default-mode/control-network recruitment.

## Repository contents

```
.
├── <your-notebook-name>.ipynb   # Full pipeline: preprocessing, modeling, visualization
├── report.pdf                   # Project paper (optional)
├── figures/                     # Exported figures (optional)
└── README.md
```

Replace the notebook name above with your actual file name.

## How to run

1. Clone the repository.
2. Install dependencies:
   ```bash
   pip install numpy scipy pandas scikit-learn matplotlib seaborn nilearn
   ```
3. Download the NMA HCP task-fMRI data and set the data path in the first cells of the notebook.
4. Run the notebook top to bottom.

The exact package list may differ; check the import cells in the notebook and adjust.

## Limitations and future work

- Small sample (100 subjects, ~20 in the test set), so performance estimates have high variance.
- Reaction time is a noisy target (attention, motor variability, fatigue).
- Future directions: combine with structural connectivity (DTI), try graph-based models such as GCNs, and scale to the full HCP cohort (~1,200 subjects).

## References

1. Finn et al., "Functional connectome fingerprinting," *Nature Neuroscience*, 2015.
2. Rosenberg et al., "A neuromarker of sustained attention from whole-brain functional connectivity," *Nature Neuroscience*, 2016.
3. Avery et al., "Distributed patterns of functional connectivity predict working memory performance," *Journal of Cognitive Neuroscience*, 2020.
4. Shen et al., "Using connectome-based predictive modeling to predict individual behavior from brain connectivity," *Nature Protocols*, 2017.
5. Poldrack et al., "Establishing best practices for evidence for prediction," *JAMA Psychiatry*, 2020.
6. Sui et al., "Neuroimaging-based individualized prediction of cognition and behavior," *Biological Psychiatry*, 2020.
