# Glacier-pyESD

- Repository to house python scripts pertaining to Danish's Master thesis project.
- As a master's candidate pursuing an International Master's degree in Marine Environment (with a focus on oceanography), Danish conducted his master thesis research at the University of Glasgow, School of Geographical and Earth Sciences.
- The thesis was conducted under the supervision of Dr. rer. nat. habil. Sebastian G. Mutz; as part of his research cluster– [Climate Dynamics Lab](https://mutz.science/).

## Project Overview

The thesis investigates the hypothesized **regime shift in the climatic controls on Norwegian glaciers around the year 2000**, analyzing 11 glaciers spanning 57–72°N across Norway's maritime-to-continental gradient (Ålfotbreen, Austdalsbreen, Engabreen, Gråsubreen, Grafjellsbrea, Hansebreen, Hellstugubreen, Langfjordjøkelen, Nigardsbreen, Rembesdalskåka, Storbreen).

<p align="center">
  <img src="Py_Plots/fig01.png" alt="cumulative" width="600">
</p>

<p align="center">
  <img src="Py_Plots/fig03.png" alt="cumulative" width="600">
</p>

## Methodology

- Correlation analysis (pre-/post-2000) using DJF (accumulation) and MJJAS (ablation) seasonal windows.
- Predictors from ERA5 reanalysis (t2m, precipitation, 10m wind, SSRD, MSL) plus teleconnection indices (NAO, EA, AO) derived via EOF/PCA of MSL.
- Empirical–statistical downscaling (customized pyESD, Perfect Prognosis design) linking ERA5 predictors to NVE glacier mass-balance records.
- Predictor selection: Recursive, Tree-based, Sequential.
- Six regressors: RidgeCV, Bayesian Ridge, ARD (linear); Random Forest, Bagging, XGBoost (non-linear).
- Model ensembling via Stacking or Voting, with cross-validation.
- Coupled with CMIP6 (MPI-ESM1-2-LR) to project Ba/Bw/Bs under SSP1-2.6, SSP2-4.5, SSP5-8.5.
- Glacier dynamics, climate–glacier feedbacks, and evolving hypsometry are not represented; projections should be interpreted with this caveat.

<p align="center">
  <img src="Py_Plots/fig02.png" alt="schematic" width="600">
</p>

## Key Findings

**Climatic controls and the post-2000 regime shift**
- The NAO is the dominant driver of inter-annual winter climate and mass-balance variability in Norway, with a strong positive phase from 1980–early 1990s producing warm, wet winters (correlation between NAO and accumulation-season precipitation, Pacc: r=0.62).
- Post-2000 mass loss represents the most negative balance years on record, driven by higher ablation-season temperatures (Tabl) and a shift toward a more negative winter NAO; the Bw–NAO correlation weakens for 7 of 11 glaciers over this period.
- Increasing post-2000 correlations between Bs and Pabl, Tabl, and surface solar radiation (SSRD), along with a stronger Ba–Tabl correlation across all 11 glaciers, support a shift toward a more energy (ablation)-dominated mass-balance regime.
- A maritime–continental gradient persists: maritime Nigardsbreen shows stronger Ba correlations with Pacc (r=0.66) and NAO (r=0.45) than continental Hellstugubreen (Pacc r=0.48, NAO r=0.39). Pre-2000 Pacc correlations are consistently higher for maritime glaciers (Ålfotbreen r=0.81, Austdalsbreen r=0.89, Hansebreen r=0.84) than continental glaciers (Hellstugubreen r=0.64, Gråsubreen r=0.44).

**Model performance**
- Across 6 regressors and 3 feature selectors, models performed best for Bw and Ba, and worst for Bs:
  - **Bw**: R² range −1 to 0.75 (mean ≈ 0.45, RMSE ≈ 0.59 m w.e.); ARD+Rec and BYR+Rec gave the highest median R² (0.30, 0.25).
  - **Ba**: R² range 0 to 0.8 (mean ≈ 0.55, RMSE ≈ 0.64 m w.e.); BYR+Tree and ARD+Rec gave the highest median R² (0.48, 0.50).
  - **Bs**: median R² range −0.3 to 0.25 (mean ≈ 0.28, RMSE ≈ 0.90 m w.e.) — consistently the hardest component to predict.
- Linear regressors (Ridge, Bayesian Ridge, ARD) consistently outperformed non-linear regressors (Bagging, Random Forest, XGBoost) across all three mass-balance components.
- Models for maritime glaciers (Ålfotbreen, Hansebreen, Engabreen, Rembesdalskåka) consistently outperformed continental glaciers (Gråsubreen, Hellstugubreen, Storbreen), consistent with Mutz et al. (2016).
- Data-quality constraints affected performance: Grafjellsbrea, trained on only 7 years of data, showed poor skill (RMSE > 1.0) across components.
- Performance is comparable to similar ESD studies in Austria (Schöner and Böhm, 2007), Norway (Mutz et al., 2016), and the Andes (Mutz and Aschauer, 2022), and generally exceeds the Andes models due to a larger NVE training dataset and simpler regional climate structure.

**CMIP6-coupled projections (MPI-ESM1-2-LR, SSP1-2.6/SSP2-4.5/SSP5-8.5)**
- Nigardsbreen and Engabreen are projected to sustain their mass through 2100; Ålfotbreen and Rembesdalskåka show little net change; the remaining seven glaciers show sustained mass loss ranging from approximately −25 m w.e. (Hellstugubreen) to over −60 m w.e. (Langfjordjøkelen) by century's end.
- Differences across the three SSP scenarios are modest for all glaciers.
- Nigardsbreen (46.6 km²) and Engabreen (36.02 km²) are among Norway's largest outlet glaciers (Andreassen and Winsvold, 2012); despite projected mass stability, both continue to show terminus retreat.
- Langfjordjøkelen's pronounced projected loss is linked to its narrow, low-lying elevation range (302–1050 m a.s.l.) and its Arctic setting, an area projected to warm rapidly by 2050 (Andreassen et al., 2012).
- Predictions broadly align with Mutz et al. (2016), except for Nigardsbreen and Ålfotbreen, which are more positive in this study — attributed to differences in training data, a more comprehensive ESD approach, and the use of CMIP6 (MPI-ESM1-2-LR, with revised radiative transfer and cloud microphysics; Mauritsen et al., 2019) rather than CMIP3.
- Glacier dynamics, glacier–climate feedbacks, and evolving hypsometry are not represented in the model framework, and projections should be interpreted with this caveat in mind.

## Source Code Modifications

Changes were made to pyESD's source code to adapt it for glacier applications:
- Recognizing mass-balance variables (Ba, Bw, Bs) as valid predictands
- Incorporating a `YearlyStandardizer` to handle the annual resolution of glacier mass-balance measurements
- Additional adjustments to support CMIP6-coupled projection workflows
