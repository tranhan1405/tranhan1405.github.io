---
title: 'Evaluation of Acoustic Response to Impact Sound Levels in Building Structures - Korean Standard'
date: 2024-06-25
permalink: /posts/2024/06/blog-post-8/
tags:
  - Impact sound
  - Building Acoustics
  - Standard
---

## How to Evaluate Acoustic Response to Impact Sound Levels in Building Structures

### Introduction
Impact sound transmission in buildings is a crucial factor for acoustic comfort. Accurately assessing impact sound levels requires a structured methodology that considers various influencing factors, including background noise. This post outlines a detailed procedure to evaluate the acoustic response to impact sounds, facilitating reliable classification of impact sound levels in building structures.

### Step 1: Input Impact Sound Measurement Data
First, measure the sound pressure levels at five distinct measurement points for each of the five tapping points. This setup ensures comprehensive data collection across the testing area.

- **Measurement Setup**: Tapping points are indicated by red arrows, and measurement points are represented by blue circles.

### Step 2: Adjust for Background Noise Influence
To account for the influence of background noise, corrections are applied if the difference between the background noise level and the measured sound pressure level falls between 6 dB and 15 dB. The corrected sound pressure level is calculated using the following formula:

$$
L = 10 \log \left( 10^{L_{sb}/10} - 10^{L_b/10} \right)
$$

where:
- $$ L $$ is the corrected sound pressure level in dB,
- $$L_{sb} $$ is the measured sound pressure level including background noise in dB,
- $$L_b $$ is the background noise level in dB.

### Step 3: Calculate Average Energy Level of Maximum Sound Pressure for Each Tapping Point
After measuring, average the maximum sound pressure levels from the five measurement points on the floor below using the following formula:

$$
L_{i, F_{\text{max}}, j} = 10 \log \left( \frac{1}{m} \sum_{k=1}^m 10^{L_{F_{\text{max}}, k}/10} \right)
$$

where:
- $$L_{i, F_{\text{max}}, j} $$ represents the average maximum sound pressure level at the \(i\)-th tapping point,
- $$L_{F_{\text{max}}, k} $$ is the maximum sound pressure level at the \(k\)-th microphone position.

### Step 4: Calculate Average Sound Pressure Level for Each Tapping Point
Next, average the sound pressure levels for all tapping points to find the final impact sound level using the following formula:

$$
L_{i, F_{\text{max}}} = 10 \log \left( \frac{1}{n} \sum_{k=1}^n 10^{L_{i, F_{\text{max}}, k}/10} \right)
$$

where:
- $$L_{i, F_{\text{max}}} $$ is the average maximum sound pressure level for the \(i\)-th tapping point.

### Step 5: Apply A-weighting
Adjust the calculated impact sound levels using A-weighting factors according to the table provided (Table 2.1), which aligns the measurements with human auditory perception.

**Table 2.1** A-weighting factors by frequency

| Frequency | 50 Hz | 63 Hz | 80 Hz | 100 Hz | 125 Hz | 160 Hz | 200 Hz | 250 Hz | 315 Hz | 400 Hz | 500 Hz | 630 Hz |
| --------- | ----- | ----- | ----- | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ |
| **One-third octave bands A(dB)** | -30.3 | -26.2 | -22.4 | -19.1 | -16.2 | -13.2 | -10.8 | -8.7  | -6.6  | -4.8  | -3.2  | -1.9  |
| **Octave bands A(dB)** |  | -26.2 |  | -16.2 |  | -8.7  |  | -3.2  |

### Step 6: Calculate Final Impact Sound Level Using A-weighted Values
Use the A-weighted sound pressure levels to compute the final impact sound level, as shown in the following formula:

$$
L_{iA, F_{\text{max}}} = 10 \log \left( \sum_{j} 10^{(X_{i, F_{\text{max}}, j} + A_j)/10} \right)
$$

where:
- $$X_{i, F_{\text{max}}, j} $$ is the maximum sound pressure level for each frequency band,
- $$A_j $$ is the A-weighting correction factor.

### Step 7: Grade Based on Average Final Impact Sound Level
Finally, classify the floor impact sound level based on the calculated final impact sound level using the grading criteria provided in Table 2.2.

**Table 2.2** A-weighted Maximum Impact Sound Level Grades

| Grade | A-weighted Maximum Impact Sound Level $$L_{iA, F_{\text{max}}}$$ |
| ----- | ------------------------------------------------------ |
| 1     | $$\leq 37 $$                                         |
| 2     | $$37 < L'_{iA, F_{\text{max}}} \leq 41 $$            |
| 3     | $$41 < L'_{iA, F_{\text{max}}} \leq 45 $$            |
| 4     | $$45 < L'_{iA, F_{\text{max}}} \leq 49 $$            |

### Conclusion
This detailed procedure ensures precise measurement, adjustment, and classification of impact sound levels in buildings, contributing to enhanced acoustic comfort. By following these steps and applying the outlined formulas, researchers and engineers can accurately evaluate and classify the impact sound levels in various building environments.

#### References
- International Standard Organization. ISO 140-7: "Acoustics - Measurement of sound insulation in buildings and of building elements - Part 7: Field measurements of impact sound insulation of floors."
- Korean Standard KS F 2863: "Field measurement of floor impact sound insulation."











