Breast Cancer Detection in Mammograms using YOLOv8

This project implements a YOLOv8-based object detection model for identifying breast tumors in mammogram images. The entire workflow was developed and executed using Google Colab notebooks.

📂 Dataset Description

The project uses the CBIS-DDSM (Curated Breast Imaging Subset of the Digital Database for Screening Mammography) dataset, available publicly on Kaggle
.

This dataset contains  mammogram images stored in JPG format, along with CSV annotation files that describe the Region of Interest (ROI) segmentation for each image. Each ROI corresponds to a localized area where a tumor is present.

There are two categories of abnormalities represented in this dataset:

Mass lesions

Calcifications

In this study, only mass lesions were considered. Calcifications were excluded in alignment with the methodology of the reference paper on which this work is based:

A YOLO-Based Model for Breast Cancer Detection in Mammograms

This choice allows for a more direct comparison with the results and scope of the cited research.

⚙️ Project Workflow

The project consists of a series of Google Colab notebooks, each responsible for a specific stage in the pipeline. The notebooks were executed sequentially as follows:

Data Preparation

Loading the CBIS-DDSM dataset from Kaggle.

Extracting and organizing the image and CSV annotation files.

Filtering the dataset to include only mass cases.

Converting ROI segmentation information from the CSV files into bounding box annotations compatible with YOLO format.

Dataset Conversion

Splitting the data into train, validation, and test sets.

Generating YOLO-style .txt annotation files for each image.

Structuring the dataset directories following YOLOv8 requirements.

Data Augmentation (Optional)

Applying custom transformations to balance the dataset.

Oversampling malignant cases using an Albumentations pipeline to address class imbalance.

Model Training

Using the YOLOv8-nano model (lightweight version of YOLOv8).

Training the model on the prepared CBIS-DDSM dataset.

Monitoring training metrics and loss curves through Ultralytics’ built-in tools.

Model Evaluation

Evaluating model performance on the test set.

Calculating standard detection metrics such as mAP, precision, and recall.

Visualizing prediction results on sample mammogram images.

🧠 Model

The YOLOv8-nano architecture was selected for its balance between computational efficiency and accuracy, making it suitable for research and potential clinical applications where inference speed is important.

🧾 Reference

This project is based on the methodology presented in:

A YOLO-Based Model for Breast Cancer Detection in Mammograms(https://link.springer.com/article/10.1007/s12559-023-10189-6)
