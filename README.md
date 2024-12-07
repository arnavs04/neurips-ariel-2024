# NeurIPS - Ariel Data Challenge 2024
Repository for [NeurIPS Ariel 2024 Challenge](https://www.kaggle.com/competitions/ariel-data-challenge-2024) with docs, resources, and codebase for the competition.

## Team Details & Results
- **Team Name:** Markov's Chain Gang
- **Team Members:** Arnav Samal, Bibhu Sundar Mohapatra (NIT Rourkela)
- **Results:**
  - **Final Rank:** 257 out of 1,152 participants
  - **Score:** 0.5704 (based on the Gaussian Log-likelihood evaluation)

## Methodology
In this project, our goal is to predict 283 spectra values (along with their associated uncertainties) for each planet, corresponding to specific wavelengths. The following methodology outlines the key steps undertaken in processing raw sensor data, preprocessing it into a meaningful format, and subsequently performing spectra prediction.

### Data Preprocessing
The raw data for each planet consists of two main signals:
1. FGS1 Signal: A time series with a shape of [135,000, 32, 32], where each time step represents 0.1 seconds, and the spatial resolution is captured in a 32x32 grid.
2. AIRS-CH0 Signal: A time series with a shape of [11,250, 32, 356], where each time step represents 1.2 seconds, with spatial data captured in a 32x356 grid (spatial and wavelength components).

To enhance interpretability, we apply a rigorous preprocessing pipeline:

1. **Calibration of Raw Signals**
Calibration transforms raw sensor data into a meaningful format:
	• Scaling: Normalizes values to mitigate sensor scale discrepancies.
	• Dead Pixel Elimination: Removes faulty sensor readings.
	• Advanced Calibration: Applies techniques per calibration documentation.
	• Wavelength Selection: Restricts AIRS-CH0 data to 40th–321st wavelengths, corresponding to 2nd–283rd target positions.

2. **Temporal and Spatial Aggregation**
	• Temporal Aggregation: FGS1 is aggregated into [187, 32, 32], and AIRS-CH0 into [187, 32, 282].
	• Spatial Reduction: Retain center pixels, reducing FGS1 to [187, 12, 12] and AIRS-CH0 to [187, 12, 282], then average spatial dimensions:
	• FGS1: [187, 1]
	• AIRS-CH0: [187, 282]

3. **Signal Concatenation**
Processed FGS1 and AIRS-CH0 signals are concatenated into a final array of shape [187, 283], forming the basis for spectral predictions.

### Spectra Prediction
The task of spectra prediction focuses on determining a constant value, which optimizes the fit of the signal while the planet transits the star. The following steps are involved:

1. **Phase Detection**
Using the preprocessed signal, the time intervals corresponding to the planet's transit are identified:
  1. Finding the Minimum Signal: The signal is scanned within a subset range (time steps 30–140), identifying the point of minimum flux.
  2. Gradient Analysis:
     - Gradients of the signal before and after the minimum flux are computed and normalized.
     - Phase points phase1 and phase2 are determined as the start and end of the transit, based on minimum and maximum gradients, respectively.

2. **Signal Optimization**
Using the identified transit phases (phase1, phase2), the goal is to find a constant such that:
  - The adjusted signal minimizes the error of fitting a cubic polynomial.
  - The optimization problem is solved using the Nelder-Mead method to find the value of that ensures smoothness of the signal across the transit interval.

### Conclusion
The spectra predictions for each wavelength are obtained by aggregating the mean of the preprocessed signals and applying the above optimization routine. This pipeline effectively identifies the spectral characteristics of the planet by leveraging calibrated sensor data and robust mathematical modeling of the transit signal.
