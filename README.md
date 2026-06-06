# 🌍 Global Development Measurement with Clustering Analysis

> A Data Science project that uses unsupervised machine learning to classify countries into development tiers — **Developed**, **Emerging**, and **Developing** — based on 22 economic, health, tech, and environmental indicators.

---

link to view : https://global-development-measurement-clustering-project-53taol9twykv.streamlit.app/

---

## 📌 Problem Statement

Countries differ vastly across economic, social, and environmental dimensions. With 2,700+ records and 25 features, manual pattern detection is impossible at scale. This project applies automated, data-driven clustering to reveal which nations are developed, emerging, or underdeveloped.

---

## 🎯 Objectives

1. Group countries using unsupervised clustering algorithms
2. Compare **6 models**: K-Means, DBSCAN, Hierarchical, Mean Shift, GMM, KNN
3. Identify development levels: Developed / Emerging / Developing
4. Derive real-world insights on inequality, health, and internet access

---

## 📊 Dataset Overview

| Metric | Value |
|--------|-------|
| Records | 2,704 |
| Total Features | 25 |
| Features Used for Clustering | 22 |
| Columns Dropped (>90% null) | 1 |
| Source | World Development Measurement (.xlsx) |

### Feature Categories

- **Economic:** GDP, Tax Rate, Lending Interest, Tourism In/Out
- **Health:** Birth Rate, Life Expectancy (M/F), Infant Mortality, Health Expenditure
- **Tech:** Internet Usage, Mobile Phone Usage, Days to Start Business
- **Environment:** CO₂ Emissions, Energy Usage

---

## 🔧 Data Preprocessing Pipeline

```
1. Missing Values    → Ease of Business dropped (93.2% null); others → median fill
2. Type Conversion   → GDP, Tourism, Tax: $/%strings → float64
3. Outlier Capping   → IQR method on all 22 numeric features
4. Log Transform     → GDP, CO₂, Energy, Tourism → log1p
5. Scaling           → StandardScaler: μ=0, σ=1 on all features
6. Final Shape       → 2,704 × 22 | 0 nulls | 0 duplicates
```

---

## 📈 Exploratory Data Analysis

### Key Correlations (Heatmap)

| Feature Pair | Correlation |
|---|---|
| Life Expectancy Male ↔ Female | +0.977 |
| Birth Rate ↔ Population 0–14 | +0.942 |
| Energy ↔ CO₂ Emissions | +0.909 |
| Infant Mortality ↔ Life Expectancy | −0.922 |
| Birth Rate ↔ Life Expectancy | −0.864 |

### Distribution Insights
- **GDP** is heavily right-skewed — USA dominates the upper tail
- **Birth Rate** shows bimodal shape — developed vs developing split
- **Internet Usage**: most countries below 40%
- Log transformation applied to GDP, CO₂, and Energy to reduce skew

### PCA Results
- 10 components → **90% variance retained**
- PC1 alone: **46.1%** variance
- PC1 + PC2: **61.0%** variance
- 3 visible cluster clouds with outliers (high GDP / CO₂)

---

## 🤖 Models Compared

### Model Performance Summary

| Rank | Model | Type | Clusters | Silhouette / Acc | Key Strength |
|------|-------|------|----------|-----------------|--------------|
| 🥇 1 | **K-Means** | Unsupervised | 3 | **0.2844** | Best score, clean 3-cluster split |
| 🥈 2 | Mean Shift | Unsupervised | 3 | 0.2771 | Auto cluster count |
| 🥉 3 | GMM | Unsupervised | 2 | 0.2561 | Soft probabilistic boundaries |
| 4 | Hierarchical | Unsupervised | 3 | 0.2389 | Visual dendrogram |
| 5 | DBSCAN | Unsupervised | 345+ | 0.1756 | Outlier / noise detection |
| ⭐ | **KNN (Validation)** | Supervised | 3 | **95.93% Acc** | Validates K-Means clusters |

> Silhouette Score: −1 to +1. Higher = better cluster separation. All scores on 2,704 × 10 PCA-reduced dataset.

---

### Model 1 — K-Means
- **Optimal Clusters:** 3
- **Silhouette Score:** 0.2844
- **Cluster Sizes:** 1204 / 885 / 615
- **n_init:** 10 (stable centroids)
- k selected via Silhouette Analysis (tested k=2 to 11)

### Model 2 — Hierarchical Clustering
- **Silhouette Score:** 0.2389
- **Linkage Method:** Ward
- **Cluster Sizes:** 1259 / 830 / 615

### Model 3 — DBSCAN
- **Silhouette Score:** 0.1756 (Lowest)
- **eps:** 0.8 | **min_samples:** 3
- Generated 345+ micro-clusters + noise points
- Best for outlier detection, not global structure

### Model 4 — GMM (Gaussian Mixture Model)
- **Best k:** 2 (tested 2–7)
- **Silhouette Score:** 0.2561
- **Covariance Type:** full
- k=2 scores best; all k≥3 scores drop to ≤0.097
- Soft probabilistic assignment with flexible boundaries

### Model 5 — Mean Shift
- **Silhouette Score:** 0.2771 (2nd Best)
- **Bandwidth:** 3.5 (estimated: 4.28)
- **Clusters Auto-Detected:** 3
- **Cluster Sizes:** 1246 / 899 / 559

### Validation — KNN
- **Accuracy: 95.93%**
- Train: 80% | Test: 20% | k=5 | Test set: 541 samples
- Confirms K-Means clusters are real, learnable patterns — not random noise

---

## 🏆 Best Model: K-Means

**Why K-Means Wins:**

1. ✅ Highest silhouette score of all 5 models (0.2844)
2. ✅ Clean 3-cluster structure: 1,204 / 885 / 615 records
3. ✅ Validated by KNN at 95.93% — clusters are statistically meaningful
4. ✅ Cluster means clearly map to Developed / Emerging / Developing

---

## 🌐 Cluster Interpretation

| | 🌟 DEVELOPED (Cluster 2) | 📈 EMERGING (Cluster 0) | 🌱 DEVELOPING (Cluster 1) |
|---|---|---|---|
| **GDP Avg** | $195.6B | $23.3B | $14.7B |
| **Internet Usage** | 45.9% | 26.2% | 3.0% |
| **Life Expectancy** | 79.2 yrs | 74.5 yrs | 59.8 yrs |
| **Infant Mortality** | 1.10% | 1.71% | 6.50% |
| **Birth Rate** | 1.40% | 1.72% | 3.59% |

---

## 💡 Key Insights

| Insight | Gap |
|---------|-----|
| 💰 **Economic Inequality** | 13× GDP gap: Developed ($195.6B) vs Developing ($14.7B) |
| 🌐 **Digital Divide** | 15× Internet: 45.9% (developed) vs 3.0% (developing) |
| 🏥 **Health Disparity** | 6× Infant mortality: 1.10% vs 6.50% — Life exp: 79 vs 60 yrs |
| 👶 **Demographic Gap** | 2.6× Birth rate: 3.59% (developing) vs 1.40% (developed) |
| ⚡ **Energy Inequality** | 3× Energy usage: 38,345 vs 12,515 units |
| 🏢 **Business Environment** | 8 days faster to start business in developed nations (23d vs 34d) |

---

## 🛠️ Tech Stack

| Category | Tools |
|----------|-------|
| **Data & Processing** | Python 3.x, Pandas, NumPy, OpenPyXL |
| **Machine Learning** | Scikit-learn (KMeans, DBSCAN, GMM, AgglomerativeClustering, MeanShift, KNN) |
| **Visualisation** | Matplotlib, Seaborn, PCA 2D plots, Heatmaps, Boxplots |
| **Model Persistence** | Pickle (.pkl) — kmeans_model.pkl, dbscan_model.pkl, knn_model.pkl |

---

## 📁 Project Structure

```
clustering/
├── app.py                          # Main application / dashboard
├── requirements.txt                # Python dependencies
├── World_development_mesurement.xlsx  # Raw dataset
├── kmeans_model.pkl                # Saved K-Means model
├── dbscan_model.pkl                # Saved DBSCAN model
├── hierarchical_model.pkl          # Saved Hierarchical model
├── gmm_model.pkl                   # Saved GMM model
├── knn_model.pkl                   # Saved KNN validation model
├── df_features.pkl                 # Processed feature dataframe
├── df_pca.pkl                      # PCA-reduced dataframe
├── means.pkl                       # Cluster means
└── Global_Development_Clustering_Presentation.pdf
```

---

## 🚀 Getting Started

### Prerequisites
```bash
pip install -r requirements.txt
```

### Run the Application
```bash
# Clone the repository
git clone https://github.com/koushikarnuri/global-development-measurement-clustering-project.git
cd global-development-measurement-clustering-project

# Install dependencies
pip install -r requirements.txt

# Run the app
python app.py
```

---

## 📦 Requirements

Key dependencies (see `requirements.txt` for full list):
```
pandas
numpy
scikit-learn
matplotlib
seaborn
openpyxl
```

---

## ⚠️ Challenges & Solutions

| Challenge | Solution |
|-----------|----------|
| Missing Values (93.2% null in Ease of Business) | Dropped column; others filled with median |
| Mixed Data Types ($/%strings in GDP, Tourism, Tax) | Custom cleaning functions to convert to float64 |
| Scale Differences (GDP in trillions vs Birth Rate 0.007–0.053) | StandardScaler (μ=0, σ=1) |
| Optimal k Selection | Silhouette tested k=2–10; k=3 best for K-Means |
| Outliers | IQR capping + log1p transform for GDP, CO₂, Energy |
| Cluster Interpretation | Per-cluster means mapped to Developed / Emerging / Developing |

---

## 📄 License

This project is for educational purposes as part of a Data Science program.

---

*Clustering · Analysis · Insights*
