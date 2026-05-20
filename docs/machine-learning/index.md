# Machine Learning

Machine Learning is the practice of building systems that learn patterns from data without explicit programming. This section covers the journey from fundamentals through practical applications.

## What You'll Learn

By the end of this section, you'll understand:

- Core ML concepts: supervised vs. unsupervised learning
- How to build, evaluate, and improve ML models
- When to use different algorithms and techniques
- How to avoid common pitfalls (overfitting, data leakage, etc.)

## Pre-requisites

Before diving into machine learning, ensure you have:

- **Python fundamentals**: variables, loops, functions, libraries (NumPy, Pandas)
- **Google Colab or Jupyter Notebooks** for interactive coding
- **Basic understanding** of the [Math Essentials](../foundations/math-essentials.md) (linear algebra, statistics)

## Resources

If you want small videos, try the following playlist. I highly recommend it. Also check other playlists on the channel. These are very important for understanding the concepts.

- [Machine Learning by StatQuest](https://www.youtube.com/playlist?list=PLblh5JKOoLUICTaGLRoHQDuF_7q2GfuJF)

One can start also with the following courses, Try both.

- [https://developers.google.com/machine-learning/crash-course](https://developers.google.com/machine-learning/crash-course)
- [Machine Learning for Everybody – Full Course](https://www.youtube.com/watch?v=i_LwzRVP7bg&ab_channel=freeCodeCamp.org)

---

## Core Concepts: Beginner Level

### Supervised Learning

Supervised learning uses **labeled data** (input-output pairs) to train models. The model learns the mapping from inputs to outputs.

**Two main types:**

1. **Regression** - Predicting continuous numerical values
   - Example: Predict house prices based on size, location, bedrooms
   - Common algorithms: Linear Regression, SVR, Random Forest
   - Evaluation: MAE, RMSE, R² score

2. **Classification** - Predicting discrete categories
   - Example: Spam detection (spam / not spam), image classification (cat / dog / bird)
   - Common algorithms: Logistic Regression, Decision Trees, SVM, Naive Bayes
   - Evaluation: Accuracy, Precision, Recall, F1-score, ROC-AUC

**Key concept:** The model "learns" a function that maps features (X) to targets (y).

### Unsupervised Learning

Unsupervised learning works with **unlabeled data** to find hidden patterns or structure.

**Main types:**

1. **Clustering** - Grouping similar data points
   - Example: Customer segmentation for marketing
   - Common algorithms: K-Means, Hierarchical Clustering, DBSCAN
   - Challenge: How many clusters? No ground truth.

2. **Dimensionality Reduction** - Reducing number of features while preserving information
   - Example: Compress 1000 features to 50 while keeping patterns
   - Common algorithms: PCA, t-SNE, UMAP
   - Benefit: Faster training, easier visualization, reduce noise

### Train-Test Split

Always split data into:

- **Training set** (~70-80%): Used to train the model
- **Test set** (~20-30%): Used to evaluate model performance
- **Validation set** (optional): Used during training to tune hyperparameters

⚠️ **Critical:** Never let test data influence training. This causes overfitting.

### Model Evaluation Metrics

**For Classification:**

- **Accuracy**: (Correct predictions) / (Total predictions) - Use when classes are balanced
- **Precision**: Of predicted positives, how many were correct? - Use when false positives are costly
- **Recall**: Of actual positives, how many did we find? - Use when missing positives is costly
- **F1-Score**: Harmonic mean of precision and recall - Good overall metric for imbalanced data

**For Regression:**

- **MAE** (Mean Absolute Error): Average |prediction - actual|
- **RMSE** (Root Mean Squared Error): Penalizes large errors more than MAE
- **R²**: Proportion of variance explained (0-1; higher is better)

### Overfitting vs. Underfitting

| Problem | Symptom | Cause | Solution |
|---------|---------|-------|----------|
| **Overfitting** | High train accuracy, low test accuracy | Model too complex, too much training | Regularization, less features, more data |
| **Underfitting** | Low accuracy on both train and test | Model too simple | Increase complexity, add features |

### Feature Engineering

Creating better features improves model performance more than choosing a different algorithm.

**Common techniques:**

- **Scaling**: Normalize features to similar ranges (StandardScaler, MinMaxScaler)
- **Encoding**: Convert categorical variables to numbers
- **Feature creation**: Combine existing features (e.g., age groups, polynomial features)
- **Feature selection**: Remove irrelevant features

### Workflow for Building ML Models

1. **Understand the problem** - What are we predicting? Is it regression or classification?
2. **Load and explore data** - Visualize, check for missing values, understand distributions
3. **Prepare data** - Handle missing values, scale features, encode categorical variables
4. **Split data** - Separate training and test sets
5. **Choose model** - Start simple (linear regression, decision tree), then try complex
6. **Train model** - Fit on training data
7. **Evaluate** - Check performance on test set
8. **Tune hyperparameters** - Adjust model settings to improve performance
9. **Iterate** - Try different features, algorithms, or parameters

---

## Common Algorithms Overview

### Linear Regression

- **Use when:** Predicting continuous values with linear relationship
- **Pros:** Simple, interpretable, fast
- **Cons:** Assumes linear relationship; poor with non-linear data
- **Library:** `sklearn.linear_model.LinearRegression`

### Logistic Regression

- **Use when:** Binary or multi-class classification
- **Pros:** Simple, interpretable, produces probability scores
- **Cons:** Assumes linear separation between classes
- **Library:** `sklearn.linear_model.LogisticRegression`

### Decision Trees

- **Use when:** Both regression and classification; interpretability matters
- **Pros:** Handles non-linear relationships, interpretable
- **Cons:** Prone to overfitting, unstable
- **Library:** `sklearn.tree.DecisionTreeClassifier/Regressor`

### Random Forest

- **Use when:** Need robust predictions without deep tuning
- **Pros:** Handles non-linear relationships, less overfitting than single trees
- **Cons:** Less interpretable, slower prediction
- **Library:** `sklearn.ensemble.RandomForestClassifier/Regressor`

### Support Vector Machines (SVM)

- **Use when:** High-dimensional data or clear margin between classes
- **Pros:** Effective in high dimensions, memory efficient
- **Cons:** Slow on large datasets, requires feature scaling
- **Library:** `sklearn.svm.SVC/SVR`

### K-Nearest Neighbors (KNN)

- **Use when:** Small to medium datasets
- **Pros:** Simple, no training required, non-parametric
- **Cons:** Slow prediction, poor with high dimensions
- **Library:** `sklearn.neighbors.KNeighborsClassifier/Regressor`

### Naive Bayes

- **Use when:** Text classification, fast probabilistic classification
- **Pros:** Fast, good for sparse data
- **Cons:** Assumes feature independence (often violated)
- **Library:** `sklearn.naive_bayes.GaussianNB/MultinomialNB`

### K-Means Clustering

- **Use when:** Need to group data into k clusters
- **Pros:** Simple, fast, intuitive
- **Cons:** Need to specify k; sensitive to initialization
- **Library:** `sklearn.cluster.KMeans`

---

## Practical Tips for Success

1. **Start simple:** Build with simple models first (linear regression, decision tree), then try complex models only if needed.
2. **Understand your data:** Spend time exploring data before building models. Garbage in = garbage out.
3. **Feature engineering > Algorithm selection:** Better features almost always beat better algorithms.
4. **Avoid data leakage:** Test data must never influence training.
5. **Scale your features:** Most algorithms perform better with scaled features.
6. **Monitor for overfitting:** Check validation performance during training.
7. **Use cross-validation:** More robust evaluation than single train-test split.
8. **Iterate, iterate, iterate:** ML is experimental. Try different features, algorithms, parameters.

---

## Next Steps

### Machine Hack

This is a platform similar to Kaggle, but it is more focused on Indian audience. It has a lot of competitions and datasets that you can explore. Also the difficulty level of the competitions is a bit lower than Kaggle, so it is a good place to start if you are new to machine learning. Here is the link to the website: [Machine Hack](https://machinehack.com/)

### Kaggle

Once you feel comfortable with the basics of machine learning, visit Kaggle website and start exploring datasets. Kaggle is a great platform to practice your skills, participate in competitions, and learn from the community.  
They have a inbuilt code editor and you can run your code in the browser without installing anything on your local machine.
Also, they regularly host competitions where you can apply your skills to real-world problems. Here is the link to the website: [Kaggle](https://www.kaggle.com/)

### Some more links

- [Machine Learning Mastery](https://machinelearningmastery.com/blog/)
- [Awesome Machine Learning](https://github.com/josephmisiti/awesome-machine-learning)

## Maths and Statistics

Once you are comfortable with the basics of machine learning, and its libraries, you can start learning about the mathematical and statistical concepts that underpin machine learning algorithms.  
This is important to understand how the algorithms work and how to tune them for better performance.

The following youtube channel has every mathematical concept for machine learning explained in a simple way. [StatQuest with Josh Starmer](https://www.youtube.com/@statquest)

Also, there is a Youtuber named Krish Naik who has a lot of videos on machine learning, data science, and deep learning. He explains the concepts in a simple way and also provides practical examples. Here is the link to his channel: [Krish Naik](https://www.youtube.com/@krishnaik06)
