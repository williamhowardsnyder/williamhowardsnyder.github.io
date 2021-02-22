---
layout: post
title: "Word Embeddings"
date: 2021-02-02 00:00:00 -0000
categories: Word Embedding
---
# Introduction:
In natural language processing and other related disciplines, it is often useful to treat words as vectors of real numbers in some high dimensional space. One intuitive way to do this is by one hot encoding, where all words are numbered off and assiged a vector $v_i$ such that the $i$th word is all zeros except for a 1 in the $i$th coordinate. For example, if "dog" was your 3rd word it would be assigned the vector $[0 \; 0 \; 1 \; 0 \; 0\; ... \; 0]$. This is a relatively simple scheme, but as you might imagine, the number of dimensions explodes with the number of words that you're considering. This means that algorithms will run slower on this data. Moreover, it doesn't take into account the context in which words are used. Over the course of this blog post we will explore a technique called <a href="https://en.wikipedia.org/wiki/Word_embedding">word embedding</a>, that will involve representing words as vectors in a much lower dimensional space, and in a way that preserves their semantic and syntactic relationships. What this means is that similar words will be close together.

Today, we are going to look at a word embedding that uses singular value decomposition and see how it performs on the task of completing analogies. Consider the following analogy:
{:refdef: style="text-align: center;"}
*Boy is to girl as grandfather is to \_\_\_\_\_\_*
{: refdef}
Let $w_{boy}$, $w_{girl}$, $w_{grandfather}$, $w_{grandmother}$ represent the vectors that correspond to the words boy, girl, grandfather, and grandmother in the embedding. We will find that in our word embedding,
<script type="math/tex; mode=display">w_{girl} - w_{boy} + w_{grandfather} \approx w_{grandmother}</script>
Over the course of this blog post, I will explain how this can be achieved.

# The Embedding:

First, we need a way to represent words as vectors. One way to do this that seems reasonable is to take a huge corpus of text, and represent each word as the number of times every other word appears near it. This vector will capture the co-occurence relationship between words. For our purposes, we will use a Wikipedia corpus with 1.5 billion words, and store all this information in a matrix $M$. Since some words only appear once, we will store the 10,000 most common words. Now, we have a matrix $M$ such that $M_{ij}$ is the number of times the $i$th word appeared near (within 5 words of) the $j$th word.

For example, the index 12 corresponds to the word "with", and the index 3 correponds to the word "in", so $M_{12, 3}$ will store the number of times the word "with" shows up within 5 words of the word "in" in our text corpus. As you might expect, the most common words will be articles and other farily non-interesting types of words. To make these words less influential we'll define a new matrix $\widehat{M} \in \mathbb{R}^{10000 \times 10000}$ such that $\widehat{M} = \ln(1 + M_{ij})$. The idea behind this operation is to smooth out the counts, and hopefully get a better embedding as a result. So, we now have a matrix $\widehat{M}$ that represents each word as a vector of real numbers, and does so in a way that captures the co-occurrence relationship between words.

TODO:
* Show that the matrix is "low rank" (i.e. only the first few are important)
* SVD can be used to efficiently find the most influential "ranks" of a matrix (those with the largest singular values)
* These correspond to 



# Performance on the Analogy Task:

Something here



# Other Interesting Properties:

Put something here too



# Notes and other stuff

We want these vectors to preserve syntactic and semantic relationships between their corresponding words so that we can use them in NLP tasks. So, for example, 

One way this can be achieved is by taking a 

A word embedding is a mapping from words to vectors of real numbers. A good word embedding is one that preserves the syntacitcal and semantic relationships between words. A natural way of testing the efficacy of word embeddings is whether it can be used to solve analogy tasks. For example, 



*This blog post was derived from lecture notes and a mini-project I completed for a course I took called CSE 422: Modern Algorithms at the University of Washington.*
