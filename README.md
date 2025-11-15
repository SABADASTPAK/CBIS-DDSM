**CBIS-DDSM Mammogram Detection Project (YOLOv8)**

This repository documents the full preprocessing pipeline, dataset preparation, and training flow for applying YOLOv8 to mass detection in the CBIS-DDSM mammography dataset. The project was fully implemented in Google Colab using multiple Jupyter notebooks.

 
 **Overview**

This project uses the Kaggle version of the CBIS-DDSM dataset, which contains JPEG images and structured CSV files derived from the original CBIS-DDSM dataset. The original dataset contains large DICOM files and cannot be downloaded or handled directly in Google Colab due to storage and download limitations. The Kaggle version provides a simplified and compressed subset suitable for deep-learning workflows.

This README summarizes:

Dataset description

Limitations of using the original CBIS-DDSM

Full preprocessing pipeline

YOLO-compatible dataset generation

Training configurations and experiments

**Dataset Description (Kaggle Version of CBIS-DDSM)**

The Kaggle version includes:

Mammogram images in JPG format (converted from original DICOM)

Six structured CSV files containing lesion metadata, ROI mask paths, and clinical information:
```

calc_case_description_train_set.csv

calc_case_description_test_set.csv

mass_case_description_train_set.csv

mass_case_description_test_set.csv

dicom_info.csv

meta.csv
```
Only mass cases were used in this project because the boundaries of masses are more defined and better suited for YOLO object detection.

Why the Original CBIS-DDSM Cannot Be Used in Google Colab

The original dataset (~163.5 GB) has the following limitations:

- Download requires NBIA Data Retriever, which cannot run on Colab

- File size exceeds Colab temporary storage limits

- DICOM format requires heavy preprocessing

- Slow download speeds in cloud environments

Therefore, the Kaggle version (~6.3 GB) is used.

🧩 Preprocessing Pipeline

The preprocessing flow consists of multiple stages, executed through several notebooks. Below is the structured documentation of all steps.

**1. 🔗 Mapping DICOM Paths to JPG Paths**

The mass CSV files reference DICOM file paths that do not exist in the Kaggle dataset. Using dicom_info.csv, three new columns were created:

- jpg image file path

- jpg ROI mask file path

- jpg cropped image file path

Notebooks:

- merge.ipynb → produces mass_train_jpg.csv

- merge_T.ipynb → produces mass_test_jpg.csv

**2.  Removing Mismatched ROI Masks*

Some ROI masks did not match their corresponding image dimensions. These rows were removed.

Notebooks:

exclude_missmatching_ROIs.ipynb → outputs:

mass_train_jpg2.csv

excluded_rows_log_train.csv

exclude_missmatching_ROIs_T.ipynb → outputs:

mass_test_jpg2.csv

excluded_rows_log_test.csv

**3.  Extracting YOLO Bounding Boxes from ROI Masks**

Each row corresponds to a single ROI mask. Bounding boxes were extracted and added as a new column (yolo_bbox).

Notebooks:

extract_bbox.ipynb → mass_train_jpg2_bbox.csv

extract_bbox_T.ipynb → mass_test_jpg2_bbox.csv

**4.  Visual Verification of Bounding Boxes**

A visualization script confirmed correct extraction of bounding boxes.

Notebooks:

verify_correctness_of_yolo_bbox.ipynb

verify_correctness_of_yolo_bbox_T.ipynb

**5.  Assigning Unique Names to Images**

Rows were grouped by:

patient_id

left/right breast

image view

A new unique name was given to each group.

Notebooks:

Create_new_names.ipynb → train.csv

Create_new_names_T.ipynb → test.csv

**6.  Generating YOLO-Compatible Images and Labels**

For every image, a .txt label file with YOLO-formatted bounding boxes was created.

Notebooks:

images_and_labels_from_csv.ipynb → outputs images & labels for training

images_and_labels_from_csv_T.ipynb → outputs images & labels for testing

Output folders:
```
CBIS-DDSM/NEW/images
CBIS-DDSM/NEW/images2
CBIS-DDSM/NEW/labels
CBIS-DDSM/NEW/labels2
```
**7.  Ensuring No Overlap Between Train and Test**
Notebook:

find_common_new_names_in_train_and_test.ipynb

Result: No overlap detected.

**8.  Merging Train and Test for Final YOLO Training**

Train and test were merged to improve total image count.

Notebook:

merge_train_and_test.ipynb → produces train_plus_test.csv

**9.  Final Dataset Folder Reorganization**

A unified structure was created before running the YOLO dataset preparation script.

Notebook:

move.ipynb

Outputs:
```
CBIS-DDSM/NEW/IMAGES
CBIS-DDSM/NEW/LABELS
```
**Image Preprocessing**

**1. Median Filtering (3×3)**

Reduces noise while preserving tumor boundaries.

**2. CLAHE Contrast Enhancement**

Different tile grid sizes and clip limits were tested.

Optimal settings:

- Tile Grid Size = (4,4) with ClipLimit ≈ 3–4

- For softer images: (8,8) with ClipLimit = 15–25

Notebook:

different_CLAHEs.ipynb

📦 Final YOLOv8 Dataset Structure
```
dataset/
├── images/
│   ├── train/
│   └── val/
├── labels/
│   ├── train/
│   └── val/
```
Notebook:

Prepare_dataset_structure.ipynb

**YOLOv8 Experiments**
**Experiment 1 — YOLOv8n, No Preprocessing, No Augmentation**

Parameters:
```
Epochs: 100

Image Size: 640

Batch: 16

Pretrained model: yolov8n.pt (COCO)
```
Results were expectedly weak due to no preprocessing or augmentation.

Output saved to:
```
/content/drive/MyDrive/yolo_results/yolov8_custom
```
A short explanation of COCO pretraining and batch size behavior is included in the notebook and thesis.

**Summary**

This project implements a complete CBIS-DDSM preprocessing pipeline suitable for YOLOv8 mass detection. The stages include path correction, ROI verification, bounding‐box extraction, image enhancement, dataset restructuring, and model training.


This project is based on the methodology presented in:

A YOLO-Based Model for Breast Cancer Detection in Mammograms(https://link.springer.com/article/10.1007/s12559-023-10189-6)
