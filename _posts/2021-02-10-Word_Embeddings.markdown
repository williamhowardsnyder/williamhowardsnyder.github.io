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
   <img style="width:500px;" src="/assets/images/svd_visual.png" />
</div>

The first left and right singular vectors (i.e. $u_1, u_2, ...$ and $v_1, v_2, ...$) are weighted more than last because they will be the ones multiplied by the largest values in $S$. Of course there is more nuance to this idea, but the upshot is that the SVD of $A$ provides a list of ingredients for matrix $A$ that is ordered by importance (as my professor puts it). Feel free to read more about SVD <a href="https://en.wikipedia.org/wiki/Singular_value_decomposition">here</a>.

It seems reasonable that our method for assigning vectors to words picked up a lot of noise. To try to dampen this noise, we are going to use the SVD of $\widehat{M} = USV^\top$ to find a low dimensional word embedding by using the first 99 columns and rows of $U$ and $S$, respectively. However, this only makes sense if those singular values are signifigantly larger than the rest. To confirm this, we can plot the first 99 singular values.

<div style="text-align:center;">
   <img style="width:450px;" src="/assets/images/p2_part_a.png" />
</div>

As suspected, the early singular values of $\widehat{M}$ are quite large relative to the rest. This ensures that $\widehat{M}$ is close to being low-rank, and using the first 99 singular vectors and values will provide a good approximation to $\widehat{M}$ while eliminating noise that might have been introduced in the other 9901 dimensions. The last step in creating our word embedding is to normalize the rows of $U$.

Here is the algorithm creating our word embedding:
<ol>
  <li>Compute SVD of $\widehat{M} = USV^\top$.</li>
  <li>Let $U_{99}$ be the first 99 singular vectors of $U = [u_1 \; u_2 \; ... \; u_{99}]$.</li>
  <li>Normalize the rows of $U_{99}$ such that each row has a $\ell_2$-norm of 1. Let's call the result $\widehat{U}_{99}$.</li>
  <li>Return $\widehat{U}_{99}$</li>
</ol>
Let $w_i \in \mathbb{R}^{10000}$ be a word represented by the $i$th column of $\widehat{M}$. Then, the embedding of that word is the $i$th column of $\widehat{U}_{99}$, which is in $\mathbb{R}^{99}$.


# Performance:

Ok, so we've created a word embedding, but how well does it preserve the semantic and syntatic relationships between words. This is not an easy question to answer, but let's examine some interesting properties of this embedding. Let $u_1$ be the embedding of the word man, $u_2$ be the embedding of the word "woman", and $u = u_1 - u_2$. When we project the embeddings of the words <code class="highlighter-rouge">boy, girl, brother, sister, king, queen, he, she, john, mary, all, tree</code> onto this vector we see some interesting results.

<div style="text-align:center;">
   <img style="width:450px;" src="/assets/images/2c_projs.png" />
</div>

The general trend is immediately obvious: words associated with "woman" have a higher projection value, while words associated more with "man" have a lower projection value. The word "Mary", a traditionally female name, has the highest projection value out of all words. On the other end of the spectrum is the word, "brother", which, semantically, is much closer to "man" than "woman". Note that the word "tree" has a projection value close to zero, or neutral, as we would expect. While the values of some words didn't exactly match our expectation, the general trend was clear. This shows that certain directions in the embedded space do indeed correspond to specific semantic concepts.


<div style="text-align:center;">
   <img style="width:450px;" src="/assets/images/2d_projs.png" />
</div>



TODO
* Provide diagrams of performance on gendered anologies
* Explain why this is the case
* Potential downsides



# Other Interesting Properties:

Put something here too



# Notes and other stuff

Annother thing here maybe


*This blog post was inspired by a project I completed for CSE 422: Modern Algorithms, which is a course I took at the University of Washington.*
