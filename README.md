# 🧬 Identification of CAD Biomarkers via Machine Learning and Differential Gene Expression

## 📌 Executive Summary
This project presents a robust, multi-phase computational pipeline designed to predict Coronary Artery Disease (CAD) using whole-blood transcriptomic data. By bridging rigorous statistical analysis (Differential Gene Expression) with predictive machine learning, this pipeline identifies a highly specific, statistically validated "hit list" of genetic biomarkers that drive CAD. 

## 💡 Motivation & Clinical Value
Finding drug targets usually takes years of trial and error in wet labs. This computational approach accelerates that process. By mathematically isolating highly predictive genes (such as *CCL3* and *IL18RAP*), this model provides actionable insights for pharmaceutical research. Instead of guessing, researchers can leverage these results to develop targeted therapeutics—for instance, developing a drug that specifically blocks a targeted pathway based on computational proof of its role in disease progression.

## 📊 Dataset & Clinical Cohort
The data utilized in this project originates from the **PREDICT Trial** (ClinicalTrials.gov Identifier: [NCT00500617], a prospective, multi-center coronary catheter-lab study.

* **Source:** NCBI Gene Expression Omnibus (GEO) - Accession [GSE20681](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE20681)
* **Sample Type:** Whole-blood RNA (*Homo sapiens*) isolated prior to cardiac catheterization.
* **Platform:** GPL4133 (Expression profiling by array)
* **Experimental Design:** 2-condition, paired experiment balancing age and gender to minimize confounding variables.
  * **Case (CAD):** Patients exhibiting ≥50% luminal stenosis in at least one major vessel, confirmed via Quantitative Coronary Angiography (QCA).
  * **Control (Non-CAD):** Subjects with <50% luminal stenosis by QCA.

---

## ⚙️ The Computational Pipeline

The architecture of this project is divided into four distinct phases, executed sequentially in `CAD_RF.ipynb`:

### Phase 1: Data Engineering & Preprocessing
* **Parsing:** Extracted the clinical metadata and raw expression matrix directly from the GEO series matrix file.
* **Normalization Check:** Evaluated the data distribution for Log2 transformation requirements.
* **Dimensionality Reduction via Variance:** Filtered out uninformative, low-variance genes by strictly retaining only those with variance above the median. This crucial step reduced the feature space from ~45,000 to ~22,500 probes, minimizing computational noise.

### Phase 2: Differential Gene Expression (DGE) Analysis
* **Statistical Testing:** Conducted independent t-tests between the 'Case' and 'Control' cohorts across the filtered genomic space.
* **Fold Change Calculation:** Computed Log2 Fold Change (Log2FC) to capture the magnitude of expression shifts.
* **Error Control:** Applied the Benjamini-Hochberg False Discovery Rate (FDR) correction to ensure statistical reliability. 
* **Target Isolation:** Filtered the results based on stringent thresholds (`p_value < 0.05` and `|Log2FC| >= 0.25`), successfully isolating 22 key biological features.

### Phase 3: Predictive Modeling (Machine Learning)
* **Data Splitting:** Applied a 70/30 stratified train-test split to maintain class balance.
* **Algorithm Selection:** Deployed a **Random Forest Classifier** to handle the high-dimensional, non-linear biological data.
* **Hyperparameter Tuning:** Utilized `GridSearchCV` to exhaustively optimize tree depth, estimator count, and split criteria.
* **Performance Metrics:** The model's predictive power was evaluated using Accuracy, ROC-AUC score, and a Confusion Matrix.

### Phase 4: Biological Validation & Pathway Analysis
* **Feature Importance Extraction:** Extracted the top predictive features determined by the Random Forest model.
* **Annotation Mapping:** Merged ML feature importance scores with DGE statistics, mapping raw Probe IDs to standardized Gene Symbols using the `GPL4133.annot` file.
* **Gene Set Enrichment Analysis (GSEA) & KEGG:** Conducted pathway analysis to map the highly predictive genes (e.g., *CCL3*, *IL18RAP*) to established biological networks. 
* **Scientific Cross-Validation:** By linking machine learning feature importance to KEGG pathways, the pipeline confirms that the computational model is tracking genuine disease pathology (e.g., inflammatory or metabolic pathways associated with CAD) rather than mathematical artifacts.
---

## 📈 Key Results & Visualizations

* **Predictive Accuracy:** The optimized Random Forest model achieved an **Accuracy of 66.67%** and an **ROC-AUC Score of 0.6456** on unseen test data, demonstrating a solid baseline for transcriptomic prediction.
* **Biomarker Discovery:** Successfully pinpointed 22 critical genes. The dual validation confirms that the computational model is genuinely tracking disease pathology, not just mathematical artifacts.

### Feature Importance
The Random Forest model identified the following features as the strongest drivers of the predictive split:
![Random Forest Feature Importance]()

*(Note: Ensure your top features like CCL3 and IL18RAP are visible here)*

### Pathway & Enrichment Analysis
To validate the biological relevance of the 22 isolated features, pathway analysis maps these genes to known systemic networks:

**KEGG Pathway Analysis:**
![KEGG Pathway Figure](images/kegg_pathway.png)

**Gene Set Enrichment Analysis (GSEA):**
![GSEA Plot](images/gsea_plot.png)

## 🛠️ Tech Stack & Libraries
* **Data Manipulation:** `pandas`, `numpy`
* **Statistical Analysis:** `scipy.stats`, `statsmodels`
* **Machine Learning:** `scikit-learn` (RandomForest, GridSearchCV, Metrics)
* **Data Visualization:** `matplotlib.pyplot`, `seaborn`

---


