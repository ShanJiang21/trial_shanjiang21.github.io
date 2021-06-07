---
layout: post
title: NLP = No loser Permitted?
date: 2019-12-09 19:32:24.000000000 +09:00
author: Shan J.
tags:
    - NLP
---


How will you represent the semantic meanings of word in a sentence, sounds challenging, and you may have multiple ways to go right?

### Curse of dimensionality

Humans have rich representations of words that let us extract the features, the simplest might be the one-hot representation, it treats the word completely independent of each other. For example, the feature of word in “**word** is‘dog’ ” is as dis-similar to “**word** is ‘thinking’ ” than it is to “**word** is ‘cat’ ”.

The dense-feature representation tried to solve this problem by representing the core feature with a vector. Each feature corresponds to several input vector entries. No explicit encoding of feature combinations. One NLP scholar have shared the summarization of feature extraction magic in the field. 

Instead of seeking the help from external knowledge base, just vector addition and subtraction with cosine similarity is enough for solving the problem. This trick works in a few different flavors:

### Word Embedding Flavors

* SVD-based vectors
* Word2vec
* GloVe (hybrid) 

The Word embeddings have already become the de facto standard in NLP and the vector representations are: 

* Distributed: Rather than sparse in the linear model, distributed throughout all the indices; 
* Distributional: The word distribution is derived from a corpus.

### Basic Naming rules in Data Science

In Columbia, courses with certain key words are pretty popular these days. Especially these come with statistical learning and modelling tutorials which not only gives hands-on experience of programming language exploratory analysis, but also crucial mindset for data science in practice.

1. **Deep Learning**: In neural network models, when we have more than one hidden layer, then it is said that we are having a deep network, so we call the architecture ***Deep Learning***.
2. **Machine Learning**: 
3. **Data Mining**: is considered the process of extracting useful information from a vast amount of data. It’s used to discover new, accurate, and useful patterns in the data, looking for meaning and relevant information for the organization or individual who needs it. 


### Experiment for social studies

Even though as a statistics student, I don't trust the surveys being conducted these days.

In medical and pharmaceutical area, clinical trial designs have their official guidelines and stringent rules for experiment design, data collection and causal inference, statistics students pay equal attention to both models and the assumptions of the models. 

But in the social science field where I was in and plan to return, things are not playing like that. 

Many projects are lack of rigorous design, there are few people thoroughly mulled over for the operationalization of the research. The transformation from a big-picture paradigm to the solid statistical analysis plan is never easy. 

In clinical trials, the time taken for a new drug to be proved is usually ten years, after stepping down the Phase I, Phase II and Phase III trial, and by going through each section of the clinical trials, we can find that some features of the drug are being compared with the existing active controls or placebos. 

Cause it matters for life, for most early phase trial patients, the newly-invented drug stands for the final glimmer signs of life and it is the tiny superiority that makes a difference. 

To be honest, the hard scientist's scoff or prejudice on social science does make a little sense, if to evaluate by the methods adopted. 

Why? 

- What is the proportion of studies that are still in use of the survey methods in the social science research? 

- How many of them are rigorously designed, evaluated? 

- What are the result of bias or prices that we have to pay for these false conclusions or seemingly *significant* results?

I still remeber the shocking truth of interpretation of $$R^2$$ during one of my biostatistical methods class, in my college, one of my teacher said to us, the usual $$R^2$$ falls under 0.2 in the sociological research, while in that case study we got 0.67 or so for $$R^2$$ and my fellow classmates take it for granted, some of them even think that is too low to be a fairly acceptable model.

Hey, bro. If you have ever looked at the harsh life of statistician in social science,  you will know how lucky you are for now.



### The Frustration towards the subject itself 

Learning statistics or jump into medical field does strike my hope and faith in the science, cause what we are trying to grasp can only be merely a part of the whole system. The hopeless experience comes naturally for medical students and social science students. 

The 

People skip, fake and lie to questions, they give whatever the society wants to hear about and sometimes they go too cared nothing about the 





