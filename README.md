# Drone fault detection from flight telemetry: IBM × ESILV hackathon (UAV track)

Team 14 · November 2025 · 5 days · built on IBM watsonx · team lead: Alexis Abou Jaoude

## Problem

Drones are used for logistics, inspection and rescue, where an undetected propeller fault can end a
mission or a flight. The challenge: from raw onboard sensor data, detect anomalies and identify the
fault, as a first step toward predictive maintenance.

## Data

Flight recordings in MATLAB `.mat` files (DronePropA-style naming: `F{fault}_SV{severity}_SP{speed}_t{trajectory}`),
each with three signal groups:

- **commander**: reference positions and setpoints
- **qdrone**: measured state (position, orientation, IMU, motor commands)
- **stabilizer**: flight mode over time

Labels come from the file name: fault type, severity, speed level and trajectory.

## Pipeline (`BI_Pipeline_UAV.ipynb`)

1. **Extraction.** Parse each `.mat` file, align the three signal groups on time, attach the labels
   from the file name and export one table (`DronePropA_Full_Extraction.csv`).
2. **Cleaning.** Interpolate and back/forward-fill missing sensor values.
3. **Feature engineering.**
   - position and yaw errors against the reference trajectory;
   - velocity, acceleration and speed magnitude;
   - vibration and stability indicators from IMU signals;
   - motor symmetry features (imbalance between actuators).
4. **Modelling.** Standardize the features and train a multi-output Random Forest that predicts fault
   type, severity, speed level and trajectory at once.

## Results

In 5 days, the team delivered a working end-to-end prototype on IBM watsonx: raw `.mat` telemetry
in, a single model out that predicts fault type, severity, speed level and trajectory together,
and a pitch to an industry jury.

We also tested the model on a flight it had never seen during training. That check showed the
model needs more flights per fault class to generalize across flights. It shaped the roadmap below.

## Next steps

- Split train and test by flight rather than by time step, and add flights per fault class
- Classify windows of signal (for example 1–2 s, with frequency-domain features) instead of single samples
- Report per-class recall, since a missed fault costs more than a false alarm
- Move from offline classification to streaming anomaly scores for predictive maintenance

## Stack

Python · pandas · NumPy · scikit-learn · seaborn / matplotlib · IBM watsonx
(notebook environment) · Google Colab
