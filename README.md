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

## Dataset & Classes
The pipeline relies on a COCO-format dataset split into training, validation, and testing sets.

### Class Mappings
The system maps original granular annotations to specific model targets:

| Original Data | YOLO Class (Detection) | 
| :--- | :--- | :--- |
| **ICCMS_RA1, RA2, RA3** | `RA` |
| **ICCMS_RB4** | `RB` |
| **ICCMS_RC5, RC6** | `RC` | 

| Original Data | Classifier Class (Fine-Grained) | 
| :--- | :--- | :--- |
| **ICCMS_RA1, RA2** | `ICCMS_RA2` |
| **ICCMS_RA3** | `ICCMS_RA3` |
| **ICCMS_RB4, RC5** | `ICCMS_RC5` |
| **ICCMS_RC6** | `ICCMS_RC6` |

## Usage
- The entire pipeline is encapsulated in the 1__COMBINED_PIPELINE.ipynb notebook.
- Data Setup: Update the DATA_ROOT and COCO_ANN_PATH variables in the notebook to point to your dataset location.
  1. Run the Notebook: The notebook performs the following sequentially:
  2. Data Splitting: Generates or loads split_manifest.json.
  3. YOLO Prep: Converts COCO annotations to YOLO format.
  4. Yoining: Fine-tunes a YOLOv8m model on the dataset.
  5. Cropping: Generates lesion crops based on ground truth for Stage 2 training.
  6. Classification Training: Trains the ResNet34-UNet model on the crop dataset.
  7. Evaluation: Runs the full inference pipeline (Detect -> Crop -> Classify) on the test set and calculates metrics
 
## Pipeline Details
- Stage 1: Detection (YOLOv8)
  1. Model: YOLOv8m (Medium) pre-trained on COCO.
  2. Config: Images resized to 640x640.
  3. Augmentation: Default YOLO augmentations.
  4. Objective: Accurately draw bounding boxes around all visible lesions.
- Stage 2: Classification (ResNet34-UNet)
  1. Model: U-Net with a ResNet34 encoder (pre-trained on ImageNet).
  2. Input: Cropped images of lesions (resized to 224x224) with CLAHE preprocessing.
  3. Loss Function: Combined Focal Loss + Dice Loss to handle class imbalance.
  4. Inference: The model outputs a spatial probability map; the class is determined by pooling probabilities over the spatial dimensions.
 
## Results
The notebook generates visualization of predictions including:
  - Ground Truth boxes with labels.
  - Predicted boxes with Stage 2 classification labels.
  - Precision, Recall, and F1-scores for both detection and classification stages.
