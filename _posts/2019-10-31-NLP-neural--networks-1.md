---
layout: post
title: NLP neural networks
date: 2019-10-31 23:32:24.000000000 +09:00
author: Shan Jiang
tags:
    - Text Analysis
    - Computational Social Science 

---

<script src='https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.5/MathJax.js?config=TeX-MML-AM_CHTML' async></script>
[TOC]

### Neural Networks and NLP

#### Word Vectors

>  Feline to cat, hotel to motel? Love and like? there are around 13 miliion English language, are they are unrelated ? Definitely not!

The hard task for us converts to the translation problem, how can we represent these word tokens into some vector that represents a point in some sort of "word” space, like *sample space* in probability. The intuition behind this is that perhaps there actually exists some **N-dimensional space** (such that N $$\approx$$ 13 million) that is sufficient to encode all semantics of our language. 

---

 The term **"one-hot”** comes from digital circuit design, meaning "a group of bits among which the legal combinations of values are only those with a single high bit (1) and all the others low (0).

---

Represent every word as an vector with  all 0s and one 1 at the index of that word in the sorted english language.

 $$R^{|V|×1}$$

We represent each word as a completely independent entity.  This word representation does not give us directly any notion of similarity. 

For this class of methods to find word embeddings (otherwise known as word vectors), we first loop over a massive dataset and accumulate word co-occurrence counts in some form of a matrix X, and then perform Singular Value Decomposition on X to get a USVT decomposition. 







### Application

`Beautiful Soup` is a library that makes it easy to scrape information from web pages.

However, if you are transiting from `Python 2` to `Python 3`, then you might need to change your code a little bit:

#### Load packages 

```python
from bs4 import BeautifulSoup ##For python 3
from urllib.request import urlopen
import re
```

```python
from nltk import word_tokenize
from nltk import bigrams
from nltk import trigrams
from nltk import ngrams
```

#### Data scapping 

You can either use `urlopen` or `request.get` while the latter one consist of two steps 

```python
## Source from Yale law center
url1  = urlopen('http://avalon.law.yale.edu/19th_century/gettyb.asp').read()

```

```python
url2 = raw_input("Enter a website to extract the URL's from: ")
r  = requests.get("http://" +url2)
data = r.text

```

```python
## text cleaning
soup = BeautifulSoup(url)
text = soup.p.contents[0]
text_1 = text.lower() 
text_2 = re.sub('\W', ' ', text_1)
```

#### Wrangling Web data

<script src='https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.5/MathJax.js?config=TeX-MML-AM_CHTML' async></script>



**Standardized Computing, Standardized Research**

>  Greatest scientific discovery of 20th Century: Powerful personal computer (standardize science) 
>
>  1956: Aound $10,000  per megabyte 
>
>  2015: $$<<$$ $0.0001 per megabyte





