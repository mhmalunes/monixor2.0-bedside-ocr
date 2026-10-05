# Monixor 2.0: Smartphone-Based Multiparameter Bedside Monitor OCR Pipeline

Monixor 2.0 is an end-to-end, smartphone-friendly optical character recognition (OCR) and computer vision system designed to automate the transcription of patient vital signs directly from bedside multiparameter monitor displays.

By replacing rigid spatial-template matching with a label-aware, proximity-based extraction architecture, the pipeline eliminates human transcription errors and cuts documentation turnaround time from 3-5 minutes down to under 6 seconds per image while maintaining clinical-grade digit accuracy.

---

## Key Highlights and Results

- High Accuracy: Achieved an overall 93.9% exact digit sequence accuracy and an average F1-score of 0.942 across diverse monitor models and varying hospital ambient lighting.
- Fast Execution: Average processing latency of 5.39 seconds (Mindray BeneView T8) and 5.81 seconds (Mindray BeneVision N17) per transaction.
- Adaptive Layout Invariance: Employs dynamic label-token proximity association rather than hardcoded coordinate bounding boxes, allowing the system to generalize across varying monitor models and layout configurations.
- Clinical Feasibility: Validated against a ground-truth dataset across multiple capture angles (Direct, Skewed Left/Right/Up/Down) and lighting conditions (Natural, Low Light, Glare).

---

## System Architecture and Pipeline

The pipeline accepts an unconstrained smartphone photograph and executes a multi-stage computer vision workflow:

1. Preprocessing and Enhancement:
   - Image resizing (max dimension 1920px)
   - Canny edge detection and screen contour extraction
   - Perspective transformation and homography deskewing
   - Luminance correction via CLAHE
   - Specular highlight and glare inpainting

2. Dual-Stage OCR Detection and Filtering:
   - Scene text detection and recognition (EasyOCR)
   - Color-space filtering (HSV waveform and background suppression)
   - Fuzzy string matching on vital sign labels (Levenshtein distance)

3. Spatial Association and Post-Processing:
   - Directional proximity matching (pairing labels with nearest numbers)
   - Clinical boundary validation against physiological ranges
   - Dynamic fallback re-OCR for low-confidence regions

4. Deployment Architecture:
   - Client-server model with a lightweight static frontend and an isolated inference backend
   - Backend: Flask inference service containerized and targeted for GPU-accelerated cloud deployment (Hugging Face Spaces / Linux cloud environment)
   - Frontend: Responsive interface hosted via GitHub Pages for direct client-side camera access and nurse verification

---

## Extracted Vital Sign Parameters

- Heart Rate (HR, ECG): 40 - 300 bpm
- Oxygen Saturation (SpO2, PLETH): 70 - 100%
- Pulse Rate (PR): 40 - 240 bpm
- Non-Invasive Blood Pressure (NIBP): 50-260 / 30-150 mmHg
- Mean Arterial Pressure (MAP): 40 - 160 mmHg
- Respiratory Rate (RR, Resp): 4 - 60 brpm
- Temperature (Temp, T1): 32.0 - 42.0 deg C

---

## Repository Structure

- pipeline_api.py: Flask REST API server implementing preprocessing and OCR pipeline
- app.py: Application entrypoint and service configuration
- pipeline.ipynb: Research notebook for pipeline prototyping and evaluation
- index.html: Responsive mobile-first UI for clinical capture and verification
- batch_eval.py: Automated benchmarking script measuring per-vital accuracy and latency
- README.md: System documentation

---

## Getting Started

### 1. Prerequisites
- Python 3.9 or higher
- pip package manager

### 2. Installation
git clone https://github.com/mhmalunes/monixor2.0-bedside-ocr.git
cd monixor2.0-bedside-ocr
pip install -r requirements.txt

Core dependencies: opencv-python, easyocr, flask, numpy, Levenshtein, Pillow.

### 3. Running the API Server
python pipeline_api.py

Runs locally at http://localhost:5000.

### 4. Running the Web Client
Open index.html in any modern web browser or serve it via a local static file server to test image capture and verification.

---

## Benchmark and Evaluation

Run automated batch evaluation across test datasets:
python batch_eval.py --dataset ./dataset/beneview_t8 --ground_truth ground_truth.csv

The script computes:
- Digit sequence exact match accuracy per parameter
- F1-scores, Precision, and Recall across angles and lighting variants
- Execution latency metrics across pipeline stages

---

## Project Context and Validation

Developed in collaboration with clinical staff at the Philippine General Hospital (PGH) under research oversight and institutional clearance from the UP Manila Research Ethics Board (UPM REB).
