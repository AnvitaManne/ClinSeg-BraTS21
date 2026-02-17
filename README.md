# ClinSeg-BraTS21: Training and Clinical Segmentation Pipeline

This project implements a full-stack medical AI workflow bridging deep learning research and clinical data infrastructure. It features a multimodal U-Net trained on the BraTS 2021 dataset and an automated inference pipeline integrated directly with a PACS server.

## Overview

The project simulates a real-world clinical deployment environment. It handles the full lifecycle of a medical imaging task, from local data management in Orthanc to automated processing and 3D validation in 3D Slicer.

Key technical components:

* Deep Learning: Multimodal U-Net architecture implemented using MONAI and PyTorch
* Clinical Integration: Automated REST API interaction with an Orthanc PACS server
* Coordinate Synchronization: Affine matrix mapping to ensure zero-shift anatomical alignment between AI masks and original DICOM scans

---

## Repository Structure

The repository separates experimental development from production-ready implementation.

`segmentation_pipeline.py`
Primary production script. Automates patient discovery, modality mapping, inference, and export in a single execution.

`development_steps/`
Modular breakdown of pipeline logic for debugging and step-by-step validation.

* `step1_check_orthanc_connec.py` – Verifies PACS server connectivity
* `step2_find_patient.py` – Locates Study UUIDs using Clinical Patient IDs
* `step3_get_patient_info.py` – Retrieves patient-level metadata
* `step4_find_modalities.py` – Automatically maps FLAIR, T1, and T2 sequences across studies
* `step5_run_model.py` – Handles DICOM transcoding, inference, and coordinate-aligned export

`model_training.ipynb`
Contains the full training workflow including data augmentation, loss function design, and hyperparameter tuning.

`models/`
Stores trained model weights (e.g., `unet_brats_multimodal_epoch_50.pth`).

`data/`
Local staging directory for temporary DICOM and NIfTI processing.

---

## Setup and Environment

### 1. Orthanc PACS Installation

The pipeline requires a local Orthanc instance to simulate clinical storage infrastructure.

* Install Orthanc Server
* Ensure it runs on port `8042`
* Configure credentials as `orthanc/orthanc`

### 2. 3D Slicer Integration

3D Slicer is used for data preparation and validation.

Transcoding
Use Slicer’s DICOM module to convert original `.nii.gz` volumes into DICOM format.

Upload
Upload converted DICOM series to the local Orthanc instance.

Verification
Load `prediction_mask.nii.gz` in Slicer alongside the source scans to verify anatomical alignment.

### 3. Python Requirements

Install dependencies for medical imaging and deep learning:

```bash
pip install -r requirements.txt
```

---

## Usage

### Automated Pipeline

Run the complete workflow:

```bash
python segmentation_pipeline.py
```

This executes patient discovery, DICOM retrieval, modality mapping, inference, and coordinate-aligned export.

### Manual Testing

For step-wise validation, run individual scripts inside `development_steps/`. Each module operates independently and allows granular verification of PACS queries, metadata extraction, or model execution.

---

## Technical Notes: Coordinate Accuracy

A common issue in medical AI deployment is spatial misalignment between model predictions and original scans. This pipeline resolves that by extracting the affine matrix and header metadata from the source FLAIR DICOM series and applying it directly to the exported NIfTI segmentation mask.

This preserves anatomical consistency in 3D space and ensures that tumor boundaries align precisely with the original imaging volume, which is critical for clinical reliability.

---

## Author

Anvita Manne
