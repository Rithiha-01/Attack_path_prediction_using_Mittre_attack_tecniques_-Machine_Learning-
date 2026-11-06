# ML Component Validation

## 1. Overview

This document describes the implemented Machine Learning components of the AI-Driven Attack Detection and Path Prediction System.

## 2. LSTM Attack Path Prediction

**File:** `src/lstm_model.py`

- Uses a two-layer LSTM neural network to predict the next MITRE ATT&CK technique.
- Converts technique sub-identifiers into parent technique identifiers to reduce vocabulary sparsity.
- Supports model training, validation-loss tracking, prediction, and multi-step forecasting.
- Saves and loads the trained model using `data/lstm.pt`.

**Validation checks:**
- Load the saved LSTM model.
- Provide a valid sequence of attack techniques.
- Check whether next-technique predictions and probability scores are returned.
- Check whether multi-step rollout produces a sequence of predictions.

## 3. Attack Flow Dataset Preparation

**File:** `src/dataset_attackflow.py`

- Retrieves the MITRE Center for Threat-Informed Defense Attack Flow dataset.
- Extracts attack paths from Attack Flow files.
- Converts paths into sequences of MITRE ATT&CK technique IDs.
- Stores generated sequences in `data/sequences.json`.

**Validation checks:**
- Check that the sequence JSON file can be loaded.
- Verify that each sequence contains a flow name and technique sequence.
- Verify that extracted paths contain at least two techniques.

## 4. Random Forest Network Flow Detection

**File:** `src/flow_detector.py`

- Uses a Random Forest classifier for network traffic classification.
- Supports CSV, Excel, and Parquet datasets.
- Maps supported traffic labels to MITRE ATT&CK techniques.
- Generates classification reports and precision information.
- Saves the trained model in `data/flow_model.joblib`.
- Uses prediction probability and minimum-flow thresholds during detection.

**Validation checks:**
- Load the saved Random Forest model.
- Check that the sample traffic dataset can be read.
- Verify that detection returns structured results when qualifying traffic is present.
- Review classification reports when training or evaluation is performed.

**Limitation:** Random row-based train/test splits may produce optimistic scores because similar traffic records can occur in both sets. Results should not be treated as proof of performance on entirely new traffic.

## 5. Live Network Flow Capture

**File:** `src/Live_flow_capture.py`

- Resolves a user-provided domain to an IP address.
- Captures packets associated with the target IP using Scapy.
- Calculates forward and backward packet and byte counts.
- Tracks TCP SYN, ACK, and FIN flags.
- Reports packet and byte rates.

**Validation checks:**
- Test domain resolution.
- Run packet capture with the required permissions.
- Verify that packet and byte statistics are displayed after capture.

**Limitation:** Live capture currently reports traffic statistics; it does not directly invoke the Random Forest detection model.

## 6. Model Artifacts and Datasets

The following ML artifacts are included in the project:

- `data/lstm.pt`
- `data/flow_model.joblib`
- `data/sequences.json`
- `data/transitions.json`
- `data/sample_traffic.csv`

## 7. Validation Status

The implementation files and model artifacts are included in the project repository. The checks listed above describe the recommended validation procedure. Actual test results and performance metrics should be recorded only after running the corresponding tests.