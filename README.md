### Analise de Dados

Data analysis can seem huge at first, but it always follows the **same basic workflow** regardless of tools, size, or domain.
Here’s the clearest possible explanation of *how to do data analysis*, step-by-step.

---

# ⭐ **The 6 Core Steps of Data Analysis**

## **1️⃣ Define the question (most important step)**

Before touching data, ask:

* What decision must be made?
* What do we want to know?
* What does “success” look like?

Examples:

* “Which customers are likely to leave next month?”
* “Which product generates the highest profit?”
* “What factors affect employee performance?”

➡️ Every other step depends on a *clear question*.

---

## **2️⃣ Collect the data**

Depending on the problem:

* Databases (SQL, PostgreSQL, MySQL, ClickHouse)
* CSV/Excel files
* APIs
* Logs
* Surveys

Tools:

* Python (pandas)
* SQL
* ETL tools (Airflow, dbt, Talend)
* Spreadsheets

---

## **3️⃣ Clean and prepare the data (80% of the job!)**

This is where most time goes.

Typical tasks:

* Fix missing values
* Handle duplicates
* Convert data types
* Standardize names
* Remove outliers
* Join multiple datasets
* Create new features (e.g., "age group", "revenue per user")

Tools:

* pandas (Python)
* SQL (SELECT, JOIN, GROUP BY, etc.)
* Excel for small datasets

---

## **4️⃣ Explore the data (EDA: Exploratory Data Analysis)**

Goal: understand the data before modelling.

Ask questions:

* What are the distributions?
* Are there correlations?
* Are there trends over time?
* Any weird anomalies?

Methods:

* Summary statistics (mean, median, std, etc.)
* Grouping and aggregation
* Correlation heatmaps
* Visualizations:

  * Histograms
  * Line plots
  * Bar charts
  * Scatter plots

Tools:

* Python (pandas + matplotlib)
* SQL
* Tableau / Power BI
* Jupyter Notebook

---

## **5️⃣ Analyze or model**

Depending on your goal:

### 🔹 **Basic Analytics**

* Rankings
* Grouped averages
* Trends
* Forecasts
* Cohort analysis

### 🔹 **Statistical Methods**

* Regression (find relationships)
* Hypothesis testing
* ANOVA

### 🔹 **Machine Learning**

* Classification (churn prediction)
* Clustering (customer segmentation)
* Time series forecasting

Tools:

* Scikit-learn (Python)
* Statsmodels
* SQL analytical functions

---

## **6️⃣ Communicate results**

The final step many beginners skip.

You must translate analysis into **actionable decisions**.

Tools:

* Power BI / Tableau dashboards
* Slides
* Reports
* Notebooks

Good communication includes:

* Clear charts
* Key findings
* Why it matters
* Recommendations

---

# ⭐ Quick Example: Sales Analysis

**Question:** Which product category had the highest revenue last year?

### 1. Load data

SQL:

```sql
SELECT * FROM sales;
```

### 2. Clean

* Convert dates
* Remove negative prices
* Fix missing customer IDs

### 3. Explore

```sql
SELECT category, SUM(amount)
FROM sales
GROUP BY category
ORDER BY SUM(amount) DESC;
```

### 4. Visualize

Bar chart by category.

### 5. Conclusion

"Category X generated €2.3M and grew 18% YoY—recommend increasing stock and ads."

---

# ⭐ Tools you can use

### If you're a beginner:

* **Excel / Google Sheets**
* **SQL**
* **Python (pandas)**

### If you're intermediate:

* **Python + Jupyter Notebook**
* **Tableau / Power BI**

### If you're advanced:

* **Spark**
* **Data warehouses (ClickHouse, Snowflake, BigQuery)**
* **Machine learning models**

---
