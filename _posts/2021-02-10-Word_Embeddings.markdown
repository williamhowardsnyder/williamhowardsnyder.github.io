---
layout: post
title: "The Power of Word Embeddings"
date: 2021-02-02 00:00:00 -0000
categories: Word Embedding
---
--# This is a test post.

A word embedding is a mapping from words to vectors of real numbers. A good word embedding is one that preserves the syntacitcal and semantic relationships between words. A natural way of testing the efficacy of word embeddings is whether it can be used to solve analogy tasks. For example, 
<script type="math/tex; mode=display">h_{t+1} = Ah_t + Bx_t~~~~~~~~~~~~~~~~~~~~~</script>









Here is my first code snippet:
{% highlight python %}
def hello_world:
  print('Hello World')
{% endhighlight %}

Pretty cool, huh!

Can I write LaTeX though?? That's the real question
$$E = mc^2$$

7:50

<script type="math/tex; mode=display">h_{t+1} = Ah_t + Bx_t~~~~~~~~~~~~~~~~~~~~~</script>

<p>We focus on time-invariant single-input single-output system. For an input sequence of real numbers $x_1,\dots, x_T\in \mathbb{R}$,  the system maintains a sequence of hidden states $h_1,\dots, h_T\in \mathbb{R}^n$, and produces a sequence of outputs $y_1,\dots, y_T\in \mathbb{R}$ according to the following rules:</p>

<script type="math/tex; mode=display">h_{t+1} = Ah_t + Bx_t~~~~~~~~~~~~~~~~~~~~~</script>

<script type="math/tex; mode=display">\quad \quad\quad~y_t = Ch_t+Dx_t+\xi_t              ~~~~~~~~~~~~~~~~(1)</script>

<p>Here $A,B,C,D$ are linear transformations with compatible dimensions, and $\xi_t$ is Gaussian noise added to the output at each time. In the learning problem, often called system identification in control theory, we observe samples of input-output pairs $((x_1,\dots, x_T),(y_1,\dots y_T))$ and aim to recover the parameters of the underlying linear system.</p>


