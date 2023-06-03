---
layout: extended-content
title: "Introduction to data science"
permalink: /osgdtraining
usemathjax: true
---

__Last updated: May 2023__


<i style="font-size: 16px; line-height: 1.0; font-weight: 50">These interactive lecture notes, and accompanying homework, are part of an introductory course in data science that I taught for <a href="https://www.osgdigitallabs.com/">Oxford Strategy Group Digital</a>, a student-run society at the University of Oxford that helps students learn data science skills by delivering digital consultancy projectsfor real companies. The objective of the course was to distill as many data science skills as possible within 5 sessions. While limited in terms of the practical skills that it can deliver, it should teach the basic intuition and vocabulary!</i>

__Lecture 1: introduction to data wrangling__

The first lecture concerns the crucial ingredient of data science: __data__. We explore how to manipulate datasets using the ubiquitous *pandas* library in Python, and how to answer interesting questions using statistical descriptors and basic plots. We also introduce *data imputation*, a part of data cleaning where we deal with missing values. We introduce these concepts using the [Titanic dataset](https://www.kaggle.com/c/titanic) popularised at Kaggle, and propose homework using the famous [Boston housing dataset](https://www.cs.toronto.edu/~delve/data/boston/bostonDetail.html), which is also an opportunity to discuss the effect of dataset bias..


- [Interactive lecture notes](https://colab.research.google.com/drive/1wwAAbgC3y6lF9VksFKMuhUOK7CYU7adT?authuser=1)
- [Homework](https://colab.research.google.com/drive/1QjUaJQDKY_968aiYdCVZsREzvR7-Ri9I?authuser=1)

__Lecture 2: introduction to modelling__

The second lecture introduces __data-driven modelling__, which I would like to define as the pursuit of using known data to predict the properties or unseen data. We introduce the concept of loss functions, and how we can find the optimal parameters of a model by minimising the value of this loss function. We then discuss model evaluation, overfitting, and how to make sure that we know our model generalises by testing it on a heldout dataset. These concepts are introduced using very simple machine learning models: linear regression and logistic regression. In the homework, we use the logistic regression to predict survival based on the Titanic dataset.

- [Interactive lecture notes](https://colab.research.google.com/drive/1a6_J2TzUhPdYZ0g1GtQzd3ylXpX9Kl6B?authuser=1)
- [Homework](https://colab.research.google.com/drive/1TluZPsjVTWTihNjCjbGy3MiKUnyZpnmZ?authuser=1)

__Lecture 3: common machine learning models__

The third lecture explores some simple, but incredibly powerful, machine learning models: nearest neighbours, Gaussian processes, and decision trees. These models are all relatively simple (if you delve into the theory — which we are not doing here —, you can understand what is going on fairly easily), yet widely used and incredibly powerful. Nearest neighbours can work with all sort of stuff, and despite their simplicity power state-of-the-art technologies such as [vector databases](https://www.pinecone.io/learn/vector-database/). Gaussian processes enable estimating the errors in our predictions, so we have a measure of our model's confidence before making decisions. Decision trees (and their advanced versions, such as random forests or boosting machines) are incredibly general and can tackle all kinds of complex data.

- [Interactive lecture notes](https://colab.research.google.com/drive/1eXkTgjgIZqaFdqV5L35epYekvQufxSOe?authuser=1)

__Lecture 4: a taste of neural networks__

The fourth lecture explores how to deal with __complex data__. The previous three lectures are focused on *feature-based data*, where we describe our data with simple features (for example, Titanic passengers based on their age, where they embarked, their wealth, etc.); however, many interesting domains such as computer vision, natural language processing, and others, do not lend themselves to featurization. In this lecture we give a introduction to neural networks and show how to use them in two problems: classifying images of handwritten digits, and determining the sentiment behind Amazon reviews.

- [Interactive lecture notes](https://colab.research.google.com/drive/11ncfZQkfiIB_eKtCmoIVmIqzzRpNiz5A?authuser=1)

__Last session: data challenge__

The final session introduced no new concepts and was focused on solving a practical problem: predicting the likelihood that a patient has breast cancer from *fine-needle aspirate cytology* (FNAC) samples.

- [Data challenge](https://colab.research.google.com/drive/1WVfRZBxrC6M2x1PP217TGSwqBZIxlhyu?authuser=1)
