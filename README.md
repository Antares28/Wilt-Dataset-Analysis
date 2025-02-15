# Wilt-Dataset-Analysis

## 1. Wilt Dataset Introduction

### 1.1 Wilt Dataset

The Wilt dataset originates from a remote sensing study *'**A hybrid pansharpening approach and multiscale object-based image analysis for mapping diseased pine and oak trees**'* [1] The study introduces an advanced classification method for detecting diseased trees (Japanese oak wilt and Japanese pine wilt disease) in high-resolution multispectral satellite imagery. 

The Wilt dataset consists of image segments generated by segmenting pansharpened images. These segments contain spectral information from the multispectral bands of QuickBird satellite imagery and texture information from the panchromatic (Pan) image band. The dataset includes a training set (4,339 samples) and a test set (500 samples). In the training set, the "diseased tree" class has significantly fewer samples (74), whereas the "other land cover" class has a much larger number of samples (4,265). Wilt dataset URL: http://archive.ics.uci.edu/ml/datasets/Wilt

### 1.2 Wilt' Dataset

The Wilt' dataset originates from research by Guilherme O. Campos et al. [2], which refines the original Wilt dataset for anomaly detection purposes. Wilt' dataset URL: https://www.dbs.ifi.lmu.de/research/outlier-evaluation/DAMI/semantic/Wilt/Wilt_05.html

Wilt' Dataset Overview:

- **Size**: 4,839 records with 5 dimensions (excluding the ID and outlier label attributes).
- **Attributes**:
  - **GLCM_pan**: Mean value of the Gray-Level Co-occurrence Matrix (GLCM) in all directions for the panchromatic (Pan) band.
  - **Mean_Green**: Mean spectral value for the Green band.
  - **Mean_Red**: Mean spectral value for the Red band.
  - **Mean_NIR**: Mean spectral value for the Near-Infrared (NIR) band.
  - **SD_pan**: Standard deviation of the panchromatic (Pan) band.

## 2. Exploratory Analysis on Wilt' Dataset

### 2.1 Box-Plot

![image-boxplot](image/image-boxplot.png)

- **GLCM_pan**: Slightly dispersed, approximately normally distributed, with a large median; the distribution of outliers is relatively balanced.
- **Mean_Green**: Downward aggregation is very obvious, with a small median; outliers are concentrated on the larger side.
- **Mean_Red**: Downward aggregation is very obvious, with a small median; outliers are concentrated on the larger side.
- **Mean_NIR**: Relatively dispersed, closer to a normal distribution, with scattered outliers on the larger side.
- **SD_pan**: Slightly dispersed, with certain outliers distributed on the larger side.

### 2.2 Statistical Features

| Attribute     | GLCM_pan | Mean_Green | Mean_Red | Mean_NIR | SD_pan   |
| ------------- | -------- | ---------- | -------- | -------- | -------- |
| Max Value     | 1        | 1          | 1        | 1        | 1        |
| Min Value     | 0        | 0          | 0        | 0        | 0        |
| Mean          | 0.692136 | 0.065948   | 0.042569 | 0.290767 | 0.156426 |
| Median        | 0.695553 | 0.058931   | 0.033065 | 0.286039 | 0.148225 |
| Variance      | 0.005386 | 0.001335   | 0.001613 | 0.010741 | 0.004698 |
| Standard Dev. | 0.073387 | 0.036533   | 0.040159 | 0.103638 | 0.068539 |

The median and mean of GLCM_pan are very close, while the variance of Mean_Green and Mean_Red is small. The Mean_NIR data is more dispersed in the dataset.

### 2.3 Scatter Plot & Histogram

![image-scatterplot](image/image-scatterplot.png)

![image-histogram](image/image-histogram.png)

### 2.4 Heatmap

![image-heatmap](image/image-heatmap.png)

Mean_Green and Mean_Red have a high correlation.

## 3. Data Cleaning

- **Data Deduplication**
- **Missing Value Handling**: Delete data objects with missing values exceeding half of the total, and fill remaining missing values with the mean.
- **Noise Detection and Handling**:
  - Based on Normal Distribution: Since the p_value is very small, the data does not follow a normal distribution, making this method unsuitable for outlier detection.
  - Box Plot: Based on the 1.5 times interquartile range, calculate the corresponding upper and lower values for attributes, and remove data with more than two attributes as outliers.

## 4. Anomaly Detection

| Method      | DBSCAN-1 (Based on Original Data) | DBSCAN-2 (Based on PCA Reduced Data) | LOF    | Decision Tree | Random Forest |
| ----------- | --------------------------------- | ------------------------------------ | ------ | ------------- | ------------- |
| Recall Rate | 91.44%                            | 15.16%                               | 31.50% | 82.35%        | 90.19%        |
| Precision   | 10.38%                            | 3.07%                                | 8.40%  | 85.71%        | 83.63%        |
| F1-Score    | 0.1864                            | 0.0511                               | 0.1326 | 0.8400        | 0.8679        |
| AUC         | 0.736                             | 0.529                                | 0.561  | 0.918         | 0.946         |

Based on the F1-score and AUC evaluation metrics, the models are ranked from high to low as follows: **Random Forest > Decision Tree > DBSCAN-1 > LOF > DBSCAN-2**.

From the perspective of supervised and unsupervised learning, supervised algorithms perform significantly better than unsupervised algorithms when prior knowledge is available. In terms of interpretability, the Decision Tree has the best interpretability, as it can directly output a tree diagram for model visualization. Regarding execution efficiency, both DBSCAN and LOF have an algorithmic complexity of O(n^2) and similar execution speeds. The Decision Tree differs in complexity from the other models, as it continuously splits based on training data. The deeper the Decision Tree, the higher the model's complexity. In this anomaly detection problem, the Decision Tree model has 10 layers and exhibits relatively high execution efficiency. Random Forest performs slower than Decision Tree in terms of execution efficiency.

## References

[1] Johnson, B., Tateishi, R., Hoan, N.. A hybrid pansharpening approach and multiscale object-based image analysis for mapping diseased pine and oak trees. *International Journal of Remote Sensing*, 2013, 34 (20), 6969-6982.

[2] G. O. Campos, A. Zimek, J. Sander, R. J. G. B. Campello, B. Micenková, E. Schubert, I. Assent, M. E. Houle. On the Evaluation of Unsupervised Outlier Detection: Measures, Datasets, and an Empirical Study. *Data*   Mining and Knowledge Discovery* 30(4): 891-927, 2016, DOI: 10.1007/s10618-015-0444-8
