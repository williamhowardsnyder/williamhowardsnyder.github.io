---
layout: post
title: "Word Embeddings"
date: 2021-03-30 00:00:00 -0000
categories: Word Embedding
---
# Introduction:
In natural language processing and other related disciplines, it is often useful to treat words as vectors of real numbers in some high dimensional space. One intuitive way to do this is by one hot encoding, where all words are numbered off and assiged a vector $v_i$ such that the $i$th word is all zeros except for a 1 in the $i$th coordinate. For example, if "dog" was the 3rd word in your vocabulary it would be assigned the vector $[0 \; 0 \; 1 \; 0 \; 0\; ... \; 0]$. This is a relatively simple scheme, but as you might imagine, the number of dimensions explodes with the number of words that you're considering. This means that algorithms will run slower when processing this data. Moreover, it doesn't take into account the context in which words are used. Over the course of this blog post we will explore a technique called <a href="https://en.wikipedia.org/wiki/Word_embedding">word embedding</a>, that involves representing words as vectors in a much lower dimensional space, and in a way that preserves their semantic and syntactic relationships.

Today, we are going to look at a word embedding that uses singular value decomposition and see how it performs on the task of completing analogies. Consider the following analogy:
{:refdef: style="text-align: center;"}
*Boy is to girl as grandfather is to \_\_\_\_\_\_*
{: refdef}
Let $w_{boy}$, $w_{girl}$, $w_{grandfather}$, $w_{grandmother}$ represent the vectors that correspond to the words boy, girl, grandfather, and grandmother in the embedding. We will find that in our word embedding,
<script type="math/tex; mode=display">w_{girl} - w_{boy} + w_{grandfather} \approx w_{grandmother}</script>
Below, I will explain how this can be achieved.

# The Embedding:

First, we need a way to represent words as vectors. Initially, it is not important that this representation is low dimensional, but it should provide some sembelance of the meaning of these words. One way to do this is to take a huge sample of text, and represent each word as the number of times every other word appears near it. This seems like a reasonable representation because words that appear in similar contexts will tend to have similar meanings. If interested you can learn more about this theory of semantics <a href="https://en.wikipedia.org/wiki/Distributional_semantics">here</a>. This vector will capture the co-occurence relationship between words. For our purposes, we will use a Wikipedia corpus with 1.5 billion words, and store all this information in a matrix $M$. Since some words are relatively unimportant and appear only once, we will store the 10,000 most common words. Now, we have a matrix $M$ such that $M_{ij}$ is the number of times the $i$th word appeared near (within 5 words of) the $j$th word.

For example, the index 12 corresponds to the word "with", and the index 3 correponds to the word "in", so $M_{12, 3}$ will store the number of times the word "with" shows up within 5 words of the word "in" in our text corpus. As you might expect, the most common words will be articles and other non-interesting types of words. To make these words less influential we'll define a new matrix $\widehat{M} \in \mathbb{R}^{10000 \times 10000}$ such that $\widehat{M} = \ln(1 + M_{ij})$. The idea behind this operation is to smooth out the counts, and hopefully get a better embedding as a result. Now that we have a matrix $\widehat{M}$ that represents each word as a vector of real numbers we can begin embedding these vectors into lower dimensions in a way that captures their meaning.

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


# Interesting Properties:

Before using the embedding to solve the analogy task, let's examine some interesting properties of this embedding. Let $u_1$ be the embedding of the word man, $u_2$ be the embedding of the word "woman", and $u = u_1 - u_2$. When we project (i.e. take the dot product of) the embeddings of the words <code class="highlighter-rouge">boy, girl, brother, sister, king, queen, he, she, john, mary, all, tree</code> onto this vector $u$ we can see some interesting results.

<div style="text-align:center;">
   <img style="width:450px;" src="/assets/images/2c_projs.png" />
</div>

The general trend is immediately obvious: words associated with "woman" have a higher projection value, while words associated more with "man" have a lower projection value. The word "Mary", a traditionally female name, has the highest projection value out of all words. On the other end of the spectrum is the word, "brother", which, semantically, is much closer to "man" than "woman". Note that the word "tree" has a projection value close to zero, or neutral, as we would expect. While the values of some words didn't exactly what we might expect, the general trend was clear. This shows that certain directions in the embedded space do indeed correspond to specific semantic concepts. This is incredible! With a relatively simple technique, we were able to extract a vector that measures the gender of our words.

Of course, you might already be suspecting the troubling consequences of this embedding. Projecting the words <code class="highlighter-rouge">math, matrix, history, nurse, doctor, pilot, teacher, engineer, science, arts, literature, bob, alice</code> onto $u$ gives us the following:

<div style="text-align:center;">
   <img style="width:450px;" src="/assets/images/2d_projs.png" />
</div>

In this plot, words like "engineer", "history", and "math" have negative projection values, which means the embedding associates them more with "man", whereas words like "nurse", "arts", and "literature" have positive projection values, which means they are associated more with "woman". The word "engineer" has the most negative value. This is probably because engineering is a male dominated field, so the words "engineer" and "man" are more likely to appear near each other in the Wiki corpus. On the other hand, "nurse" is the most positive value for a similar reason. These results reflect human biases about gender roles and could could be problematic if used in practice. For example, suppose some company used such word embeddings to extract information from candidates' resumes to improve their "search for qualified job candidates" option. An individual could be deemed more (or less) qualified for a particular position (e.g. an engineering job) on the basis of gender. So, it is always important to consider the biases that can arise from any type of algorithm before implementing it in a context with societal implications.

# Performance on the Analogy Task:

Ok, so we've created a word embedding that has some interesting properties, but how well does it preserve the semantic and syntatic relationships between words. This is not an easy question to answer, but one test we can employ is seeing how well it completes analogies. As mentioned in the introduction, the analogy task involves filling in the blank "*A is to B as X is to \_\_\_\_*", where the relationship between A and B is similar to that of X and "\_\_\_\_". A couple examples from the data set include
<ul>
  <li>*Boy is to girl as grandfather is to \_\_\_\_\_\_* where the relationship is term of the opposite gender</li>
  <li>*Phoenix is to Arizona as Miami is to \_\_\_\_\_\_* where the relationship is captial-to-state</li>
  <li>*Running is to ran as knowing is to \_\_\_\_\_\_* where the relationship is present-to-past tense</li>
</ul>
Let $w_{1}$, $w_{2}$, $w_{3}$ represent the vectors that correspond to the first, second, and third words in the analogy. We fill in the blank of these analogies by finding the word that corresponds to the nearest vector in our word embedding to the following vector:
<script type="math/tex; mode=display"> w_{2} - w_{1} + w_{3} </script>
It may seem kind of arbitrary at first to subtract off some vectors and add others, but the idea is that subtracting $w_1$ from $w_2$ will represent the difference between these two concepts. A similar difference should be seen between $w_3$ and $w_4$. So, adding $w_3$ to this quantity will put the result in the ballpark of $w_4$.

I used nearest neighbor search with cosine similarity to find the nearest neighbor. The final equation for completing the analogies was
<script type="math/tex; mode=display">\underset{i}{\text{arg min}} \frac{\widehat{M}[i] \cdot (w_{2} - w_{1} + w_{3})}{||\widehat{M}[i]||_2 \times ||w_{2} - w_{1} + w_{3}||_2} </script>

With this simple algorithm I was able to achieve an accuracy of 52%, which is surprisingly good considering how many possible words could fill in that blank.


*This blog post was inspired by a project I completed for CSE 422: Modern Algorithms, which is a course I took at the University of Washington.*
