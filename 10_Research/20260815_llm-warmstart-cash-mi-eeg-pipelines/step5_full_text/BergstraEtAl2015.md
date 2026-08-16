---
citation_key: "BergstraEtAl2015"
title: "Hyperopt: a Python library for model selection and hyperparameter optimization"
authors: "James Bergstra; Brent Komer; Chris Eliasmith; Dan Yamins; David Cox"
year: 2015
doi: "10.1088/1749-4699/8/1/014008"
source: "abstract-only"
access_level: "abstract-only"
retrieved_date: "2026-08-15"
---

# Hyperopt: a Python library for model selection and hyperparameter optimization

**Authors**: James Bergstra, Brent Komer, Chris Eliasmith, Dan Yamins, David Cox
**Year**: 2015
**DOI**: 10.1088/1749-4699/8/1/014008

> **Note / 備註**: Full text not available through automated open-access channels. Try institutional access: https://doi.org/10.1088/1749-4699/8/1/014008
> 全文未能以自動化開放管道取得，請嘗試機構存取。

---

## Abstract

Sequential model-based optimization (also known as Bayesian optimization) is one of the most efficient methods (per function evaluation) of function minimization. This efficiency makes it appropriate for optimizing the hyperparameters of machine learning algorithms that are slow to train. The Hyperopt library provides algorithms and parallelization infrastructure for performing hyperparameter optimization (model selection) in Python. This paper presents an introductory tutorial on the usage of the Hyperopt library, including the description of search spaces, minimization (in serial and parallel), and the analysis of the results collected in the course of minimization. This paper also gives an overview of Hyperopt-Sklearn, a software project that provides automatic algorithm configuration of the Scikit-learn machine learning library. Following Auto-Weka, we take the view that the choice of classifier and even the choice of preprocessing module can be taken together to represent a single large hyperparameter optimization problem . We use Hyperopt to define a search space that encompasses many standard components (e.g. SVM, RF, KNN, PCA, TFIDF) and common patterns of composing them together. We demonstrate, using search algorithms in Hyperopt and standard benchmarking data sets (MNIST, 20-newsgroups, convex shapes), that searching this space is practical and effective. In particular, we improve on best-known scores for the model space for both MNIST and convex shapes. The paper closes with some discussion of ongoing and future work.
