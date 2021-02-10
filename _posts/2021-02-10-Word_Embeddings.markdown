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
*Boy is to girl as grandfather is to ______*
{: refdef}
Let $w_{boy}, w_{girl}, w_{grandfather}, w_{grandmother}$ represent the vectors that correspond to the words boy, girl, grandfather, and grandmother in the embedding. We will find that in our word embedding,
<script type="math/tex; mode=display">w_{girl} - w_{boy} + w_{grandfather} \approx w_{grandmother}</script>
Over the course of this blog post, I will explain how this can be achieved.

# The Embedding and Optimizations:

Something here

# Performance on the Analogy Task:

Something here

# Other Interesting Properties:

Put something here too


## Notes and other stuff

We want these vectors to preserve syntactic and semantic relationships between their corresponding words so that we can use them in NLP tasks. So, for example, 

One way this can be achieved is by taking a 

A word embedding is a mapping from words to vectors of real numbers. A good word embedding is one that preserves the syntacitcal and semantic relationships between words. A natural way of testing the efficacy of word embeddings is whether it can be used to solve analogy tasks. For example, 




