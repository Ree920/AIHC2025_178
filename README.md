# Two-Stage Dental Caries Detection and Classification Pipeline

This repository contains a deep learning pipeline designed to automate the detection and classification of dental caries (lesions) from intraoral images. The system operates in two stages:
1.  **Detection (Stage 1):** Uses **YOLOv8** to localize caries lesions on tooth surfaces.
2.  **Classification (Stage 2):** Uses a **ResNet34-UNet** architecture to classify the detected lesions into fine-grained clinical categories based on ICCMS/ICDAS standards.

## Project Overview
- **Input**: Raw intraoral images.
- **Pipeline**: 
  1. Detects regions of interest (ROI) using YOLOv8.
  2. Crops the detected regions.
  3. Classifies the crops using a segmentation-based classifier.
- **Output**: Images with bounding boxes and specific class labels (e.g., RA2, RA3, RC5, RC6).

## 🗂 Dataset & Classes
The pipeline relies on a COCO-format dataset split into training, validation, and testing sets.

### Class Mappings
The system maps original granular annotations to specific model targets:

| Original Data | YOLO Class (Detection) | Classifier Class (Fine-Grained) |
| :--- | :--- | :--- |
| **ICCMS_RA1, RA2** | `RA` | `ICCMS_RA2` |
| **ICCMS_RA3** | `RA` | `ICCMS_RA3` |
| **ICCMS_RB4, RC5** | `RB` / `RC`* | `ICCMS_RC5` |
| **ICCMS_RC6** | `RC` | `ICCMS_RC6` |
