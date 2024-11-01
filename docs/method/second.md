This notebook is a fork of

- [Vitaly Kudelya](https://www.kaggle.com/vitalykudelya): [NeurIPS - Ariel Data Correlation Parallel + Scale](https://www.kaggle.com/code/vitalykudelya/neurips-ariel-data-correlation-parallel-scale)

**The idea**: we can scale not only the **s** values, but the corresponding **sigma** values as well (using the same prior distribution *wave_to_apriori_scale*). 

This notebook builds upon the excellent work done in the following baselines: <br />
- [Sergei Fironov](https://www.kaggle.com/sergeifironov): [ariel_only_correlation](https://www.kaggle.com/code/sergeifironov/ariel-only-correlation)
- [Laurent Pourchot](https://www.kaggle.com/pourchot): [Ariel Data Challenge 2024](https://www.kaggle.com/code/pourchot/ariel-data-challenge-2024?scriptVersionId=195970765)
- [qianc](https://www.kaggle.com/xiaocao123): [Ariel Data Challenge 2024](https://www.kaggle.com/code/xiaocao123/ariel-data-challenge-2024)

<!-- Thank you to the authors for sharing their insights and code 🙏 -->

## Granularity
Granularity of predictions is one planet.
For each planet we need to predict `283` `spectras` (float number) for corresponding wave lengths and each with an associated uncertainty `sigma`.

## Data
For each planet we have:
- **FGS1** signal (`FGS1_signal.parquet`) `[135000, 32, 32]` (`135000` time steps, each representing `0.1` seconds and `32*32` is sensor data) 
- **AIRS-CH0** signal (`AIRS-CH0_signal.parquet`) `[11250, 32, 356]` (`11250` time steps, each representing `1.2` seconds and `32*356` is sensor data `32` spatial and `356` different wave lengths)

Also we have additional calibration data to make raw data from sensors more informative.

## Baseline overview
    
### Preprocessing
* Calibration of raw signal. It consists of scaling the signal, eliminating dead pixels and other complicated techniques [calibration discussion](https://www.kaggle.com/competitions/ariel-data-challenge-2024/discussion/528066). Only `40-321` wave_length positions (they should relate to `2-283` positions in targets) are taken for **AIRS-CH0**
* Calibrated signal is divided into time chunks and aggregated (mean). It reduces number of time points and we get
  `[187, 32, 32]` for **FGS1** (`187` time steps) and `[187, 32, 282]` for **AIRS-CH0** (`187` time steps)
* Only center spatial pixels are taken and get `[187, 12, 12]` for **FGS1** and `[187, 12, 282]` for **AIRS-CH0**
* Aggregate (mean) sensor data per special dimension. We get `[187, 1]` for **FGS1** and `[187, 282]` for **AIRS-CH0**
* Concatenate **FGS1** and **AIRS-CH0** to `[187, 283]` array. Let's call it a `preprocessed_signal`

### Spectra prediction
* Aggregate (mean) of our `preprocessed_signal` for `2-283` wave_length positions and get `[187]` data points (time dimension) as an input to further optimizations
* Find time points `phase1` and `phase2` - start and end of planet going in front of the star (`phase_detector`)
* Next we need to find a constant `s` (actually our further prediction of `spectra`) that after multiplication
`signal[phase1:phase2] * (1 + s)` we can't see a "step" in the signal (more formally, the error after fitting 3nd power polynomial is minimal)

An exmaple for planet signal phases is demonstrated at the image
![signal_phases.png](/neurips-ariel-2024/docs/imgs/signal-phases.png)
* We just predict the same `s` for all different spectras (`283`)
* Sigma is just a constant `0.000145` for all spectras and all planets




## Updates
* Paralllel preprocessing of planet's raw data with `pqdm` (the library for parallel and interactive bar computations similar to `tqdm`). It made preprocessing x2.2 faster on my tests
* It was added a simple multiplication of `spectra` predicitons for different wavelength based on it under or over predictions on average for train data
* A little refactoring

