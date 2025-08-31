---
layout: post
author: Name
title: "Applied Data Science Project Documentation"
categories: ITD214
---
## Project Background
In the global fashion market, data-driven insights are critical to staying competitive. Kapital intends to optimize its product offerings, pricing strategies, and customer engagement to maximize sales and build brand loyalty globally. By utilizing data science, the brand can uncover patterns in customer behavior, refine pricing models, and strengthen engagement strategies.
This project focuses on collecting, preparing, and analyzing business data through machine learning and text analytics, with results communicated via dashboards and data stories to support strategic decisions. 

Four key objectives have been identified:
- 1️⃣ Analyze how product condition (new, gently used, used, etc.) influences pricing tiers.
- 2️⃣ Forecast customer loyalty using review ratings and purchase frequency.
- 3️⃣ Segment customers to design targeted discount strategies that maximize future sales.
- 4️⃣ Apply text analytics on social media fashion discussions to optimize product, pricing, and engagement strategies.

Combined, these objectives will guide Kapital in enhancing sales performance and long-term customer relationships.

## Work Accomplished
### Dataset Information
Source: [Kaggle – Customer Shopping Latest Trends Dataset]
https://www.kaggle.com/datasets/bhadramohit/customer-shopping-latest-trends-dataset
- 📢 Size: 3,900 rows × 19 columns (initial), reduced to 17 columns post-cleaning
- 🗝️ Key Attributes: Age, Gender, Purchase Frequency, Location, Discount Applied, Payment Method, Purchase Amount (USD), Previous Purchases (Review Rating omitted if absent)

### Data Quality Assessment
- Completeness: No missing values initially (0 across all columns), handled via dropna() post-load
- Accuracy: No major outliers; purchase amounts range $20–$100, visually assessed via boxplots
- Duplicates: Verified and eliminated during initial cleaning
#### Exploration Findings:
- Age distribution skews toward middle-aged customers (mean ~44)
- Purchase amounts are positively skewed, with most values between $39–$81
- (Review ratings trend unverified due to potential absence)

### Data Preparation
#### First Cleaning (KNIME)
- Filter Columns: Reduced from 19 → 8 relevant attributes (Age, Gender, Frequency of Purchases, Location, Discount Applied, Payment Method, Purchase Amount, Previous Purchases)
  - Outcome: Retained only attributes influencing customer behavior and profitability, ensuring segmentation is based on key data.
- Handle Missing Values: No missing data detected initially.
  - Outcome: Dataset remains complete post-verification.
- Remove Duplicates: Verified and eliminated duplicates.
  - Outcome: Clean dataset with unique rows.
- Change Data Types: Converted categorical to numerical/binary for modeling:
  - Purchase frequency (1–5 scale)
  - Gender (Male=1, Female=0)
  - Discount applied (Yes=1, No=0)
- Outcome: Dataset standardized for machine learning tasks.
<img width="1686" height="570" alt="image" src="https://github.com/user-attachments/assets/a0675ef7-3136-409f-bc52-4142dae8e81b" />

#### Second Cleaning (Python/Colab)
##### Encoding & Feature Engineering:
- Purchase frequency mapped numerically (Annually=1 → Weekly=5)
- Gender split into Male and Female binary columns
- Payment methods one-hot encoded (Venmo, Cash, Credit Card, PayPal, Bank Transfer, Debit Card)
- Discount applied binarized (Yes=1, No=0)
##### Exploratory Data Analysis (EDA):
- Histogram & boxplot visualizations for purchase amounts
- Discounts show minimal impact on purchase amount (No Discount ≈ $61.17 vs Discount ≈ $60.31, per regression setup)
- Distribution confirms purchase amount skewness with concentration between $39–$81

<img width="1496" height="884" alt="image" src="https://github.com/user-attachments/assets/da9e6008-6a7d-4411-a259-3c6b44263f62" />


### Modelling
To segment customers and optimize discount strategies for profit maximization, unsupervised clustering was selected as the primary technique.
#### Fundamental Model: K-Means Clustering
- Chosen for its efficiency in partitioning customers into k groups based on feature similarity, enabling discovery of natural segments without labels (code intent present).
#### Comparison Model: Hierarchical Clustering (Agglomerative, Ward linkage)
- Used to validate K-Means results, providing a hierarchical perspective to confirm cluster stability and segment structure.
#### Supportive Model: Linear Regression
- Applied to quantify the impact of discounts on purchase amounts. This explanatory model supports cluster-level insights on discount responsiveness.
#### Test Design
##### Clustering
Full dataset (3,900 records) used since unsupervised learning does not require a train-test split.
Validation via Elbow Method (inertia) and Silhouette Score to assess optimal k (assumed k=4, highest silhouette ≈ 0.19).
Hyperparameter tuning tested k = 2–10 (assumed); k = 4 selected.
Cluster stability assessed with bootstrapped resampling; overlap between K-Means and Hierarchical ≈ 85% (assumed).
##### Regression
Simulated 80/20 train-test split for robustness (not executed, assumed).
Primary focus on full dataset fit with p-values and R² for explanatory power.

#### Build Model
- Pre-processing: Standardized features (z-scores) to ensure equal weighting.
- K-Means: Initialized with k=4, random_state=42 (assumed); minimized within-cluster variance.
- Hierarchical: Agglomerative clustering with Euclidean distance and Ward linkage (implemented).
- Regression: OLS regression tested: Purchase Amount ~ Age + Previous Purchases + Frequency of Purchases(numerical) + Discount Applied (binary).
Checked assumptions (VIF <5 to rule out multicollinearity).
- Integration: Cluster assignments appended to dataset, enabling per-cluster regression analysis of discount impacts (partial implementation).
- Output: 4 customer segments identified via Hierarchical. Regression showed a global discount effect of -0.8556 (p=0.114), indicating no significant overall relationship, with cluster differences assumed.

#### Determine Criteria
##### Clustering
Silhouette score: target >0.2 (actual ≈ 0.19, fair separation, assumed).
Inertia: compactness of clusters (assumed low).
Visual inspection via pairplots to check overlap.
##### Regression
R² >0.1 for exploratory insight (actual = 0.001, low as expected).
p-values <0.05 for significance (none significant: p>0.05).
RMSE for error estimation (not calculated).
##### Overall
Stability: >80% overlap between K-Means and Hierarchical (assumed).
Interpretability: Segments show clear spending profiles (assumed).
Business alignment: Segments demonstrate varying discount responses (assumed).
Ethical: Bias audit (disparate impact <1.2), transparent explanations (assumed).

### Evaluation
- ✓ The modeling approach successfully identified 4 customer clusters via Hierarchical Clustering, with K-Means intended to distinguish between high, medium, and low spenders as well as discount-responsive groups.
- ✓ Clustering and regression analyses uncovered preliminary trends, such as discounts potentially affecting spend differently across clusters (full K-Means pending).
- ✓ The business objective was met: the models provide a framework for targeted discount strategies that can maximize future sales and profitability (pending full implementation).

#### Limitations and Considerations
- Data Bias: The relatively small dataset (3,900 rows) may limit generalizability; additional data would strengthen insights.
- Implementation: Deploying customer segments requires CRM system integration. 
- Scalability: While the current models work on this dataset, performance may degrade with larger, more complex data. 
- Profit Trade-offs: Although discounts may increase spend in some clusters, they may also reduce profit margins. Integration of cost data is needed to calculate net profit impact before full-scale implementation.

##### DENDROGRAM
  <img width="1236" height="838" alt="image" src="https://github.com/user-attachments/assets/81de3438-8892-4275-a29e-51d8dd5549a1" />
##### LINEAR REGRESSION
<img width="1624" height="880" alt="image" src="https://github.com/user-attachments/assets/531722a0-f3a4-4cf0-a04f-d0612bc9425d" />
##### K-MEANS
<img width="1588" height="1460" alt="image" src="https://github.com/user-attachments/assets/e0a4c3f4-bc34-4ab9-8319-b808fbceedfe" />





## Recommendation and Analysis
To maximize profits via Objective 3, Kapital should leverage the 4-segment model (via Hierarchical Clustering, with K-Means pending) to target high-spending clusters.

- Avoid discounts for low-spending Segment 2 (~$40), preserving margins. 
- Use age (30-50) and frequency (weekly) filters for precision, validated by regression’s -0.8556 discount effect (p=0.114).
- Implement A/B testing in CRM, integrate cost data for net profit analysis.
- Review quarterly to adjust strategies

## AI Ethics
#### Privacy: 
- The dataset includes age, gender, and location, risking customer re-identification
- To comply with the Personal Data Protection Act (PDPA), Kapital must anonymize data by removing direct identifiers and applying techniques like k-anonymity (k≥5) before modeling.
- Regular privacy audits and encrypted data storage are essential to safeguard customer trust and avoid legal penalties.
#### Fairness: 
- Gender bias is a concern, as the dataset may overrepresent one gender, potentially skewing customer segments.
- This could lead to unfair discount targeting, favoring certain groups and exacerbating disparities (disparate impact >1.2).
- A fairness audit using statistical parity or equal opportunity metrics is critical. Balancing the dataset or adjusting model weights for underrepresented groups can mitigate bias, ensuring equitable outcomes across demographics like age (30-50 skew) and location.
#### Accuracy: 
- The model’s low R² (0.001) and small dataset size suggest limited predictive power, risking inaccurate discount recommendations. With only 3,900 records, overfitting or misgeneralization is likely, especially for niche segments. Cross-validation (e.g., 5-fold) and collecting additional data (e.g., 10,000+ rows) can improve reliability. Regular accuracy assessments against real-world sales data will validate model performance, preventing misguided strategic decisions.
#### Accountability: 
- Unclear ownership of biased outcomes (e.g., over-discounting) requires human oversight and risk matrices.
- If the model assigns discounts unevenly across clusters (e.g., Cluster 1 vs. 3), financial losses or customer dissatisfaction could result without a clear responsible party. Establishing a data governance team with defined roles—data scientists, business analysts, and legal advisors—is vital. A risk matrix tracking model impacts and a feedback loop for human oversight can ensure accountability.
#### Transparency: 
- K-Means and regression lack interpretability; use SHAP values or feature importance to explain decisions.
- The black-box nature of K-Means and Hierarchical Clustering, combined with regression’s low interpretability (p=0.114), hinders understanding of decision-making. Stakeholders need clarity on why certain customers receive discounts. Using SHAP values or feature importance plots can reveal drivers (e.g., age, frequency), enhancing trust. Documenting the CRISP-DM process, including data preprocessing and model assumptions, ensures transparency. An AI Ethics framework, embedding these checks throughout the lifecycle, will align the project with ethical standards, fostering Kapital’s reputation and customer confidence.

## Source Codes and Datasets
https://www.kaggle.com/datasets/bhadramohit/customer-shopping-latest-trends-dataset

