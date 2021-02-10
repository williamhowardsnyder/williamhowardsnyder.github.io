---
layout: post
title: "The Power of Word Embeddings"
date: 2021-02-02 00:00:00 -0000
categories: Word Embedding
---
# Introduction:
A word embedding is a mapping from words to vectors of real numbers. In natural language processesing, it is often useful to use word embeddings because then we can employ  all the tools from linear algebra that allow us to extract information from matrices. If the word embedding is good enough (i.e. captures the relevant semantic and syntatic relationships between words), then these techniques can be used to solve various tasks on natural language.

Today, we are going to look at one particular word embedding and see how it performs on the task of completing analogies. Consider the following analogy:
{:refdef: style="text-align: center;"}
*Boy is to girl as grandfather is to \_\_\_\_\_\_*
{: refdef}
Let $w_{boy}$, $w_{girl}$, $w_{grandfather}$, $w_{grandmother}$ represent the vectors that correspond to the words boy, girl, grandfather, and grandmother in the embedding. We will find that in our word embedding,
<script type="math/tex; mode=display">w_{girl} - w_{boy} + w_{grandfather} \approx w_{grandmother}</script>
Over the course of this blog post, I will explain how this can be achieved.

# The Embedding and Optimizations:

First, we need a way to represent words as vectors. I have absolutely no training in linguistics, but one way to do this that seems reasonable is to take a huge corpus of text, and represent each word as the number of times every other word appears near it. This vector will capture the cooccurence relationship between words. For our purposes, we will use a Wikipedia corpus with 1.5 billion words, and store all this information in a co-occurence matrix $M$. To account for words that appear only once, we will only store the 10,000 most common words. Now, we have a matrix $M$ such that $M_{ij}$ is the number of times the $i$th word appeared near (within 5 words of) the $j$th word.

For example, the index 12 corresponds to the word "with", and the index 3 correponds to the word "in", so $M_{12, 3}$ will store the number of times the word "with" shows up within 5 words of the word "in" in our text corpus. As you might suspect, the most common words will be articles and other farily non-interesting types of words, to account fo this we define a new matrix $\widehat{M} \in \mathbb{R}^{10000 \times 10000}$ such that $\widehat{M} = \ln(1 + M_{ij})$. The idea behind this operation is to smooth out the counts, and hopefully get a better embedding as a result.

So, we now have a matrix $\widehat{M}$ that represents each word as a vector of real numbers, and does so in a way that captures the realtionships between words. This is technically a word embedding, but we can do better. It is computationally intensive to run algorithms on matrices of this size. One way to reduce the...

Next we can focus on how to make this embedding represent semantic and syntic relationships
SVD works because the Cooccurence matrix is low rank



# Performance on the Analogy Task:

Something here



# Other Interesting Properties:

Put something here too



# Notes and other stuff

We want these vectors to preserve syntactic and semantic relationships between their corresponding words so that we can use them in NLP tasks. So, for example, 

One way this can be achieved is by taking a 

A word embedding is a mapping from words to vectors of real numbers. A good word embedding is one that preserves the syntacitcal and semantic relationships between words. A natural way of testing the efficacy of word embeddings is whether it can be used to solve analogy tasks. For example, 



TODO: Figure out how to properly cite
This blog post was derived from lecture notes and a mini-project I completed for a class I took under \_\_\_\_ Lee at the University of Washington.
