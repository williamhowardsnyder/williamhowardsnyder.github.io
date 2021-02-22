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

First, we need a way to represent words as vectors. Initially, it is not important that this representation is low dimensional or captures the meaning of words. One way to do this that seems reasonable is to take a huge corpus of text, and represent each word as the number of times every other word appears near it. This vector will capture the co-occurence relationship between words. For our purposes, we will use a Wikipedia corpus with 1.5 billion words, and store all this information in a matrix $M$. Since some words only appear once, we will store the 10,000 most common words. Now, we have a matrix $M$ such that $M_{ij}$ is the number of times the $i$th word appeared near (within 5 words of) the $j$th word.

For example, the index 12 corresponds to the word "with", and the index 3 correponds to the word "in", so $M_{12, 3}$ will store the number of times the word "with" shows up within 5 words of the word "in" in our text corpus. As you might expect, the most common words will be articles and other farily non-interesting types of words. To make these words less influential we'll define a new matrix $\widehat{M} \in \mathbb{R}^{10000 \times 10000}$ such that $\widehat{M} = \ln(1 + M_{ij})$. The idea behind this operation is to smooth out the counts, and hopefully get a better embedding as a result. Now that we have a matrix $\widehat{M}$ that represents each word as a vector of real numbers we begin embedding these vectors into lower dimensions in a way that captures their meaning.

As alluded to above, the way that we are going to do this is by using singular value decomposition (SVD). The SVD of a matrix $A \in \mathbb{R}^{m \times n}$ expresses $A$ as the product of three "simple" matrices:

<script type="math/tex; mode=display">A = USV^\top</script>
where
<ol>
  <li>$U$ is an $m \times m$ orthogonal matrix (i.e. it's column vectors are perpendicular)</li>
  <li>$V$ is a $n \times n$ orthogonal matrix</li>
  <li>$S$ is a $m \times n$ diagonal matrix with non-negative entries. For convienence, these entries are sorted in descending order where the first one is the largest</li>
</ol>

<div style="text-align:center;">
   <img style="width:800px;" src="/assets/images/svd_visual.png" />
</div>

The first left and right singular vectors (i.e. $u_1, u_2, ...$ and $v_1, v_2, ...$) are weighted more than last because they will be the ones multiplied by the largest values in $S$. Of course there is more nuance to this idea, but the upshot is that the SVD of $A$ provides a list of ingredients for matrix $A$ that is ordered by importance (as my professor puts it). Feel free to read more about SVD <a href="https://en.wikipedia.org/wiki/Singular_value_decomposition">here</a>.

We are going to use the SVD of $\widehat{M} = USV^\top$ to find a low dimensional word embedding by using the first 99 columns and rows of $U$ and $S$, respectively. This only makes sense if those singular values are signifigantly larger than the rest. To confirm this, we can plot the first 99 singular values.

<div style="text-align:center;">
   <img style="width:800px;" src="/assets/images/p2_part_a.png" />
</div>

but before we do so, we should ensure that $\widehat{M}$ is low-rank. For, if all the singular values are similar, then only using the first few might be damage



TODO:
* Show that the matrix is "low rank" (i.e. only the first few are important)
* SVD can be used to efficiently find the most influential "ranks" of a matrix (those with the largest singular values)
* These correspond to 



# Performance on the Analogy Task:

Something here



# Other Interesting Properties:

Put something here too



# Notes and other stuff

Annother thing here maybe


*This blog post was derived from lecture notes and a mini-project I completed for a course I took called CSE 422: Modern Algorithms at the University of Washington.*
