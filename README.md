# 🩺 Retinal OCT Segmentation with Shallow CNN, ResNet & Graph Cut

## 📌 Overview

This project performs **retinal OCT (Optical Coherence Tomography) image segmentation** using the **Duke OCT dataset**.
We focus on two conditions: **Normal** and **Age-related Macular Degeneration (AMD)**.

Methods implemented:

* **Mask Generation** – create ground-truth masks from `.mat` files
* **Data Preparation** – splitting data into train/val/test and preprocessing
* **Shallow CNN** – custom lightweight encoder–decoder network
* **ResNet34 Segmentation** – deep encoder–decoder with transfer learning
* **Graph Cut Refinement** – post-processing to smooth CNN predictions

---

## ⚙️ Workflow

1. **Mask Generation** (`Mask_generation.ipynb`)

   * Converts Duke `.mat` files into multiclass masks (4 classes, values 0–3).
   * Saves both visualization PNGs and raw `.npy` masks.

2. **Data Preparation** (`data_preparation.ipynb`)

   * Resizes all images/masks to **256×256**
   * Splits data: **70% Train, 15% Validation, 15% Test**

3. **Model Training**

   * **Shallow CNN:** custom 3-block encoder–decoder
   * **ResNet34:** pretrained encoder with custom decoder
   * Training settings (both):

     * `BATCH_SIZE = 8`
     * `EPOCHS = 20`
     * `LR = 1e-4`
     * Loss = CrossEntropy, Optimizer = Adam

4. **Prediction & Graph Cut**

   * Generate masks from trained models
   * Apply **Graph Cut (α-expansion)** with:

     * `smooth_weight = 10`
     * `max_iter = 5`
   * Produces smoother, more accurate boundaries

5. **Evaluation Metrics**

   * **Dice Score** (per-class + mean)
   * **IoU** (per-class + mean)
   * **Precision & Recall** (per-class + mean)

---

## 📊 Results

Training and evaluation are included inside the **ResNet (AMD/Normal)** and **Shallow CNN (AMD/Normal)** notebooks.
They report **Dice, IoU, Precision, and Recall** for each experiment on the test set.

👉 After training, you can run the included evaluation functions to generate a table like this:

| Model             | Dataset | Dice (mean) | IoU (mean) | Precision | Recall |
| ----------------- | ------- | ----------- | ---------- | --------- | ------ |
| Shallow CNN + GC  | AMD     | 0.8067      | 0.7156     | 0.8423    | 0.8007 |
| Shallow CNN + GC  | Normal  | 0.7505      | 0.6385     | 0.7763    | 0.7586 |
| ResNet34 + GC     | AMD     | 0.9238      | 0.8792     | 0.9257    | 0.9235 |
| ResNet34 + GC     | Normal  | 0.9149      | 0.8683     | 0.9101    | 0.9232 |
