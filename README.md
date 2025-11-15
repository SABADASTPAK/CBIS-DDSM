# CBIS-DDSM Mammography Object Detection Pipeline (YOLOv8)

This repository contains the full preprocessing and dataset preparation pipeline used for training a YOLOv8 object detection model on the **CBIS-DDSM** mammography dataset. All steps were implemented using Google Colab notebooks. Because the original CBIS-DDSM dataset is extremely large (163.5 GB) and difficult to download in Colab, this project uses the **Kaggle version** of the dataset, where DICOM files were pre‑converted to JPG.

The pipeline includes:

* Preprocessing the Kaggle CBIS‑DDSM dataset
* Fixing broken ROI masks
* Generating YOLO‑formatted bounding boxes
* Verifying annotation correctness
* Assigning unique names to each mammography image
* Creating YOLO‑compatible directory structure
* Applying preprocessing filters (Median + CLAHE)
* Running YOLOv8 experiments

---

## 📌 1. About the CBIS‑DDSM Dataset

CBIS‑DDSM is a curated and cleaned version of the original DDSM dataset designed to solve issues such as:

* Outdated, non‑standard compressed formats
* Imprecise lesion annotations
* Difficult download and extraction process
* Lack of standardized train/test splits

### Key characteristics:

* **Patients:** 1,566
* **Images:** 10,239 DICOM files
* **Lesion types:** Mass, Calcification (only Mass used in this project)
* **Labels:** ROI mask, bounding boxes, pathology, density, BI‑RADS rating
* **License:** CC BY 3.0

### Kaggle Version Used in This Project

Since Google Colab cannot store the full dataset, Kaggle’s 6.3 GB JPG version was used. It includes:

* `mass_case_description_train_set.csv`
* `mass_case_description_test_set.csv`
* ROI masks (JPG)
* Cropped tumor images
* Metadata (`dicom_info.csv`, `meta.csv`)

---

## 📌 2. Full Preprocessing Pipeline

Below is a high‑level overview of the full data preparation flow.

### **2.1. Merge DICOM paths with JPG paths**

Some CSV files include DICOM paths that do not exist in the Kaggle version. These were replaced using `dicom_info.csv`.

Notebooks:

* `merge.ipynb` → produces `mass_train_jpg.csv`
* `merge_T.ipynb` → produces `mass_test_jpg.csv`

---

### **2.2. Remove mismatched ROI masks**

Some ROI masks did not match their image dimensions and were removed.

Notebooks:

* `exclude_missmatching_ROIs.ipynb` → train set
* `exclude_missmatching_ROIs_T.ipynb` → test set

Results:

* Train: 1318 rows → 1253 valid
* Test: 378 rows → 365 valid

---

### **2.3. Generate YOLO Bounding Boxes**

Each row corresponds to one ROI mask. Bounding boxes were extracted from ROI masks.

Notebooks:

* `extract_bbox.ipynb`
* `extract_bbox_T.ipynb`

Output files:

* `mass_train_jpg2_bbox.csv`
* `mass_test_jpg2_bbox.csv`

---

### **2.4. Visual Verification of Boxes**

Bounding boxes and masks were displayed side‑by‑side to confirm correctness.

Notebooks:

* `verify_correctness_of_yolo_bbox.ipynb`
* `verify_correctness_of_yolo_bbox_T.ipynb`

---

### **2.5. Assign Unique Names to Images**

Because one image may appear multiple times (multiple tumors), a unique name is generated using:

* `patient_id`
* laterality (LEFT/RIGHT)
* view (CC/MLO)

Notebooks:

* `Create_new_names.ipynb` → train.csv
* `Create_new_names_T.ipynb` → test.csv

---

### **2.6. Generate YOLO‑format Images and Labels**

For each unique image, a `.txt` file with YOLO bounding boxes is generated.

Notebooks:

* `images_and_labels_from_csv.ipynb`
* `images_and_labels_from_csv_T.ipynb`

Output structure:

```
CBIS-DDSM/NEW/images
CBIS-DDSM/NEW/images2
CBIS-DDSM/NEW/labels
CBIS-DDSM/NEW/labels2
```

---

### **2.7. Check Train/Test Overlap**

Ensures train and test sets are independent.

Notebook:

* `find_common_new_names_in_train_and_test.ipynb`

Result: **No overlap detected**

---

### **2.8. Merge Train and Test Sets**

Following the reference paper, train and test sets are merged to increase dataset size.

Notebook:

* `merge_train_and_test.ipynb`

Output: `train_plus_test.csv`

---

### **2.9. Organize Final Folder Structure**

Both image folders and label folders are merged.

Notebook:

* `move.ipynb`

Output:

```
CBIS-DDSM/NEW/IMAGES
CBIS-DDSM/NEW/LABELS
```

---

## 📌 3. Image Preprocessing

### **3.1. Median Filtering**

A 3×3 median filter was applied to reduce digitization noise while preserving edges.

### **3.2. CLAHE (Contrast Limited Adaptive Histogram Equalization)**

Different combinations of parameters were tested:

* Tile Grid Sizes: (2,2), (4,4), (8,8)
* Clip Limits: 2, 3, 4, 10, 15–35

**Best results:**

* (4×4) with ClipLimit 3–4 → best detail enhancement
* (8×8) with ClipLimit 15–25 → smoother output

Notebook:

* `different_CLAHEs.ipynb`

---

## 📌 4. Final YOLOv8 Dataset Structure

YOLO requires the following directory structure:

```
dataset/
 ├── images/
 │    ├── train/
 │    └── val/
 ├── labels/
 │    ├── train/
 │    └── val/
```

Notebook:

* `Prepare_dataset_structure.ipynb`

Outputs:

* Final train/val split
* Final `.yaml` configuration file

---

## 📌 5. YOLOv8 Experiments

### **Experiment 1: YOLOv8n without any preprocessing or augmentation**

**Settings:**

* Epochs: 100
* Image size: 640
* Batch: 16
* Pretrained weights: `yolov8n.pt` (COCO)

This experiment is used as a baseline and normally results in weak performance.

### Notes on YOLO Parameters

* **Batch size** controls stability, GPU memory usage, and speed.
* **Pretrained weights** (COCO) greatly reduce training time.

---

## 📌 Summary

This repository provides a complete, reproducible pipeline for preparing the CBIS‑DDSM Kaggle dataset for YOLOv8, including:

* Cleaning and fixing dataset inconsistencies
* Extracting bounding boxes from masks
* Preprocessing (median filter + CLAHE)
* Creating YOLO‑ready image/label structures
* Running initial YOLOv8 tests

This README serves as a detailed guide for anyone wishing to repeat or extend the experiments.

---

If you want, I can also:

* Add diagrams or workflow charts
* Add commands for running YOLOv8
* Create a shorter README version
* Translate the README into Persian
