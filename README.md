# Universität St. Gallen  
## Data Science Fundamentals  
### Predicting Rent Prices in Lagos, Nigeria  

**Fall Semester 2022**  
**Group 7:** Gabriel-Maximilian Bergmann, Vincent Klaer, Lukas Lehrecke, Laurenz Schneeberger  
**Professor:** Prof. Dr. Johannes Binswanger  
**Date:** 14 December 2021  

---

## Abstract  
This project aims to predict annual rent prices in Lagos, Nigeria, based on various dwelling attributes. First, we perform preliminary data analysis and pre-process the data by filtering aggressively to make it usable for statistical learning. Then, we employ GeoPandas and natural language processing to visualize the data’s geographical components. Using the cleaned data, we construct various regression models and a random forest. The efficacy of our models is assessed primarily via their R² score. The random forest shows the most predictive capacity, explaining 56% of the dataset’s variance.  

---

## 1. Dataset  
The dataset employed describes annual rent prices as of 2022 for various real estate objects in Nigeria and contains information about each object’s location, servicing status, construction date, furnishing, number of bedrooms, bathrooms, and sanitary outfittings. The data was originally extracted from the Nigerian Real Estate platform **PropertyPro NG** and is available via Kaggle; however, it has **0 code submissions and 10 overall downloads** (Pinnick, 2022).  

### 1.1 Pre-processing of Non-Geographical Data  
For our purposes, this dataset is not as clean as would be desirable. A **four-step cleaning process** allows the reduction of **98,080 observations to 4,730 highly usable samples**. First, **non-residential properties** were removed by filtering the ‘Description’ column to exclude observations with keywords such as **"Land", "Working", "Office",** or **"Joint Venture"**.  

Second, to enhance the predictive capability of the multivariate models elaborated upon later, the ‘Description’ column was filtered once more to extract features such as **"Duplex", "Detached"**, or **"Apartment"** for each observation.  

Third, rampant price dispersion in the dataset suggests incorrect entries that could lead to noisy or badly fitted predictions. **Outliers were then cleaned using a low-pass filter** at the **third quartile plus 1.5 times the interquartile range**. Using a similar method for low values does not work, as the high-pass filter would be negative. To nonetheless remove nonsensically low outliers, we make a **judgment call** and remove objects with a rent under **20,000 Naira**.  

### 1.2 Pre-processing of Geographical Data  
The fourth and final step in the cleaning process consists of **decomposing the ‘Area’ column**, which is given as **verbal data in non-standardized form**, into standardized **states, areas, and micro-locations**. This project focuses on **Lagos**, so we remove the few remaining entries not attributable to the city. As the data was extracted from a brokerage platform, the **micro-location description is highly inconsistent and often inconclusive**, making **Python-package-based coordinate extraction nearly impossible**.  

Instead, we resort to **less granular location data**, which we call **‘Area’**. Due to the lack of fitting geodata and the limited geographical representation given by singular coordinates, a **custom Polygon Shapefile was created in QGIS** that plots the areas described in the dataset. Using **GeoPandas**, the **centroid coordinates of each area were extracted** for regression purposes, while the **area Polygon data was used for plotting purposes**.  

There are, however, **limitations** to the usability of the location data. The **top map** in Figure 1 gives us a picture of rental price disparity, which might be expected in a developing country where very few select locations achieve high prices. The **bottom map** shows the **uneven distribution of observations**. Many areas do not exceed **10 observations**, while **Lekki**, the large **peninsula in the southeast**, contains **over 2,000 observations**, most of which are expected to be in the **western part of Lekki**.  

---

## 2. Prediction Models  

### 2.1 Regression Model  
This regression analysis initially **omits location as a feature** and then **adds it in later** to show how starkly the model’s performance differs when location data is incorporated. A series of regression models were tested: **OLS regression, Lasso regression, Ridge regression**, and **Elastic Net regression**. The **Ridge regression outperformed** due to noticeable **multicollinearity in the dataset** (Kidwell, Harrington-Brown, 1982).  

The most accurate naive regressions used the **‘Bedrooms’** feature to fit the model. **10-fold cross-validation** demonstrates a **mean R² score of 20% with a standard deviation of 4.4%**. These values are **nearly identical** for the **OLS, Lasso, and Ridge regressions**, while the **Elastic Net underperforms mildly**.  

A **multivariate OLS regression** using all above features as infeed allows the model to **improve** on the previously best **R² score, lifting it to 20.3% (σ = 7.1%)**. However, due to **multicollinearity**, the **Ridge regression** performs better at **R² = 21.36%**. By applying **second-degree polynomial Ridge regression**, we attain the best result: **R² = 25.67%**.  

Adding in the **‘Area’ feature** drastically increases the accuracy of the model to a **mean R² of 49.79%**, almost **doubling** the predictive capacity.  

### 2.2 Univariate Geographical Regressions  
Two univariate regressions using **distance from the city center** and **the ‘Area’ feature** were performed. The **linear regression with polynomials of degree 2** resulted in **R² = 0.0077**, which is a poor fit. However, increasing the polynomial degree improves **R² to 0.159**.  

A **20-fold cross-validation process** found that a regression with **polynomials of degree 12** had the **best R² score (0.37)**, while a **degree 9 regression had the lowest mean absolute error (₦1.5mm)**.  

### 2.3 Random Forest Regression  
A **Random Forest Regressor** was developed, which attained a **mean R² score of 0.538** using **default parameters**. Hyperparameter tuning was performed using **Grid Search** and **Bayesian Optimization**. The **optimized model achieved an R² of 0.57**.  

The **most important predictors** were:  
1. **Location**  
2. **Number of bedrooms**  

---

## 3. Project Evaluation  
The **Random Forest Regressor** emerged as the **most potent model**, explaining **57% of the dataset’s variance**. However, three **limitations** exist:  
1. **Computational constraints prevented extensive cross-validation**.  
2. **Overfitting risk** due to the feature set.  
3. **Uneven dispersion of data by location** affects generalizability.  

---

## Bibliography  
- Kidwell, J., & Harrington-Brown, L. (1982). *Ridge Regression as a Technique for Analyzing Models with Multicollinearity*. Journal of Marriage and Family, 44(2), 287-299.  
- Pinnick, E. (2022). *Nigeria Rent Prices (2022)*. Kaggle.  
- Probst, P., Boulesteix, A. L., & Bischl, B. (2018). *Tunability: Importance of Hyperparameters of Machine Learning Algorithms*. Journal of Machine Learning Research, 20.  
- Ye, A. (2020). *The Beauty of Bayesian Optimization, Explained in Simple Terms*.  
