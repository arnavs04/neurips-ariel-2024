# NeurIPS - Ariel Data Challenge 2024

## Quick Links
- [Official Website](https://www.ariel-datachallenge.space/)
- [Challenge Website](https://www.kaggle.com/competitions/ariel-data-challenge-2024)

## Timeline
- **August 1, 2024** - Start Date.
- **October 24, 2024** - Entry Deadline. You must accept the competition rules before this date in order to compete.
- **October 24, 2024** - Team Merger Deadline. This is the last day participants may join or merge teams.
- **October 31, 2024** - Final Submission Deadline.

*Note: The organizers reserve the right to update the contest timeline if necessary.*

## Description
The discovery of exoplanets—planets orbiting stars other than our Sun—has transformed our cosmic perspective, challenging conventional notions about Earth's uniqueness and the potential for life elsewhere. As of today, we are aware of over 5,600 exoplanets. Detecting these worlds is the initial step; we must also comprehend and characterise their nature by studying their atmospheres. In 2029, ESA Ariel Mission will conduct the first comprehensive study of 1,000 extrasolar planets in our galactic neighbourhood.

Observing these atmospheres is one of the hardest data-analysis problems in contemporary astronomy. When an exoplanet transits its host star in our line of sight, a tiny fraction of starlight (50–200 photons per million) passes through the planet's atmospheric annulus and interacts with its chemistry, clouds, and winds. These faint signals typically range from 50ppm (for Super-Earth like planets) to 200ppm (for Jupiter like planets) in magnitude and are regularly corrupted by the noise of the instrument. A major component of this noise is due to the inevitable vibration of the spacecraft in space, known as 'jitter noise'. This noise arises from the difficulties of maintaining precise pointing in low-gravity environments, as the spacecraft relies on spinning momentum wheels for stability. Akin to taking long-exposure images with a shaky hand, this noise poses a far greater challenge than the motion blur encountered in commercial photography applications. The photometric variation (∼200 ppm) caused by jitter noise alone is comparable to the variation exhibited by the planetary signal we aim to detect, undermining signals from small planets like Earths and super-Earths. Coupled with other sources of correlated and uncorrelated noises, it is proving difficult for us to achieve the strict technical requirement of the Ariel Payload design.

The task of this competition is to extract the atmospheric spectra from each observation, with an estimate of its level of uncertainty. In order to obtain such a spectrum, we require the participant to detrend a large number of sequential 2D images of the spectral focal plane taken over several hours of observing the exoplanet as it eclipses its host star. Performing this detrending process to extract atmospheric spectra and their associated errorbars from raw observational data is a crucial and common prerequisite step for any modern astronomical instrument before the data can undergo scientific analysis.

## Possible Approaches
This is a multimodal supervised learning task. Participants can choose to detrend this jitter noise in either modality (i.e. the image, time or spectral domains) or combinations thereof. Each modality bears different advantages. Here we outline two common training strategies.

* Approach 1: Train directly on the full 3D data cube and extract the corresponding spectra. This approach leverages the rich information content but as a consequence requires a lot of computing resources (See Image --> Spectral Domain on the above figure).
* Approach 2: Make the data lighter by summing up the fluxes along the pixel y-axis, for each wavelength, resulting in 2D images of dimension (N_times, N_wavelengths), and transform the images in order to enhance transit depth variations between wavelengths.

However, neither approach is optimal for denoising jitter time series and we anticipate the winning solutions to include information from all three domains.

## Evaluation
This competition evaluates predicted spectra (μ_user) and corresponding uncertainties (σ_user) for different wavelengths against the ground truth pixel level spectrum (y) using using the Gaussian Log-likelihood (GLL) function.

$$GLL = -\frac{1}{2}(log(2\pi) + log(\sigma^2_{user}) + \frac{(y-\mu_{user})^2}{\sigma^2_{user}})$$

The GLL values from each pair will be summed across all wavelengths and across the entire test set to produce a final GLL value (L). The final GLL value will be transformed into a score using the following conversion function:

$$score = \frac{L-L_{ref}}{L_{ideal}-L_{ref}}$$

We define L_ideal as the case where the submission perfectly matches the ground truth values, with an uncertainty of 10 parts per million (ppm). This ideal case is defined based on Ariel's Stability Requirement. For L_ref is defined using the mean and variance of the training dataset as its prediction for all instances.

The score will return a float in the interval [0, 1], with higher scores corresponding to better performing models. Any score below 0 will be treated as 0.

## Submission Requirements
- File Format: submission.csv
- File Structure: 567 columns (first column for planet_id, next 283 columns for spectra, remaining columns for uncertainties).

### Technical Constraints
- CPU Notebook: ≤ 9 hours runtime
- GPU Notebook: ≤ 9 hours runtime
- No internet access
- External data must be freely and publicly available

## Prizes
| Position | Prize Amount |
|----------|-------------|
| 1st | $15,000 |
| 2nd | $10,000 |
| 3rd | $8,000 |
| 4th | $7,000 |
| 5th | $5,000 |
| 6th | $5,000 |

## NeurIPS 2024 Workshop
- Top submissions will be invited to present at the workshop (attendance optional)
- Only teams attending the workshop are eligible for presentation slots
- Presenters must cover all travel and associated costs

## Organizing Team
- Dr. Kai Hou Yip (UCL)
- Dr. Lorenzo V. Mugnai (Cardiff University & UCL)
- Ms. Rebecca L. Coates (UCL)
- Dr. Andrea Bocchieri (Sapienza Università di Roma)
- Dr. Andreas Papageorgiou (Cardiff University)
- Mr. Orphée Faucoz (CNES)
- Ms. Tara Tahseen (UCL)
- Dr. Virginie Batista (IAP)
- Ms. Angèle Syty (IAP)
- Mr. Arun Nambiyath Govindan (UCL)
- Dr. Ingo P. Waldmann (UCL)

## Citation
```
Kai Hou Yip, Lorenzo V. Mugnai, Rebecca L. Coates, Andrea Bocchieri, Andreas Papageorgiou, Orphée Faucoz, Tara Tahseen, Virginie Batista, Angèle Syty, Arun Nambiyath Govindan, Sohier Dane, Maggie Demkin, Enzo Pascale, Jean-Philippe Beaulieu, Quentin Changeat, Pierre Drossart, Billy Edwards, Paul Eccleston, Clare Jenner, Ryan King, Theresa Lueftinger, Nikolaos Nikolaou, Pascale Danto, Sudeshna Boro Saikia, Luís F. Simões, Giovanna Tinetti, and Ingo P. Waldmann. NeurIPS - Ariel Data Challenge 2024. Kaggle, 2024.
```