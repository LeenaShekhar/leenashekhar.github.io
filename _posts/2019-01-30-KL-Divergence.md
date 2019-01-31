---
layout: post
title: Deriving KL Divergence
image: /img/kale_divergence.jpg
---

If you read/implement machine learning (and application) papers, there is a high probability that you have come across Kullback–Leibler divergence a.k.a. KL divergence loss. I frequently stumble upon it when I read about latent variable models (like VAEs). I am almost sure all of us know what the term means (don't worry if you don't as I have provided a brief explanation below and Google wil get you hundreds of resources on it), but may not have actually derived it till the end. In my opinion, deriving this term would make its implementaion much clearer. 

Below is my attempt to do so in case of univariate Gaussian distrbutions, which can be extended to the multivariate case as well [1](#References).

##What is KL Divergence?

KL divergence is a measure of how one probability distribution differs (in our case q) from the reference probability distribution (in our case p). Its valuse is always >= 0. Though, I should remind yo that it is not a distance metric as it is not symmetric. To put into equation $$KL(q || p) \not\equiv KL(p || q)$$.

$$KLD(q || p ) = Cross Entropy(q, p) - Entropy (q)$$, where $$q$$ and $$p$$ are two univariate Gaussian distributions.

More specifically,

$$
\begin{align*}

KL(q || p) &= -\int q(z) \log p(z) dz - (- \int q(z) \log q(z) dz ) \\
&= -\int q(z) \log p(z) dz + \int q(z) \log p(z) dz \\
&= \int q(z) \log \frac{q(z)}{p(z)}

\end{align*}
$$

##How to derive KL Divergence?

We know that PDF of Gaussian distribution can be written us:

$$
\begin{align*}

q(z; \mu, \sigma^2) &= \frac{1}{\sqrt{2 \pi \sigma^2}} e^-{\frac{(z - \mu)^2}{2 \sigma^2}}

\end{align*}
$$

After taking the logarithm of the PDF above we get:

$$
\begin{align*}

\log q(z; \mu, \sigma^2) &= \log (\frac{1}{\sqrt{2 \pi \sigma^2}}) + (-{\frac{(z - \mu)^2}{2 \sigma^2}}) \\
&= - \frac{1}{2} \log (2 \pi \sigma^2) - \frac{(z - \mu)^2}{2 \sigma^2}

\end{align*}
$$

Let's also assume that we have that our two distributions have parameters as follows:
$$q(z) \sim N(\mu, \sigma^2)$$ and $$p(z) \sim N(0, 1)$$. 

To add some more context in terms of latent variable models, we try to fit an approximate posterior to the true posterior by minimizing the *reverse KL divergence* (computationally better than the forward one, read more here [2](#References)). Think of $$z$$ as the latent variable, $$q(z)$$ as the approximate distribution and $$p(z)$$ as the prior distribution. Usually, we model $$q$$ and $$p$$ as Gaussian distributions. Prior distribution is assumed to have mean of 0 and variance of 1 (standard Normal distribution) and parameters of $$q$$ are the output of the inference (encoder) network.

Now, let's look at Cross Entropy and Entropy seperately for ease of evaluation. 

**Entropy**

$$
\begin{align*}

-\int q(z) \log q(z) dz &= \int q(z) [\frac{1}{2} \log (2 \pi \sigma^2) + \frac{1}{2 \sigma^2} (z - \mu)^2] dz \\
&= \frac{1}{2} \log (2 \pi \sigma^2) \int q(z) dz + \frac{1}{2 \sigma^2} \int (z - \mu)^2 q(z) dz \\
&= \frac{1}{2} \log(2 \pi \sigma^2) + \frac{1}{2} \\
&= \frac{1}{2} \log(2 \pi) + \frac{1}{2} \log(\sigma^2) + \frac{1}{2}

\end{align*}
$$

**Cross Entropy**

$$
\begin{align*}

-\int q(z) \log q(z) dz &= \int q(z) [\frac{1}{2} \log (2 \pi \sigma^2) + \frac{1}{2 \sigma^2} (z - \mu)^2] dz \\
&= \frac{1}{2} \log (2 \pi) \int q(z) dz + \frac{1}{2} \int z^2 q(z) dz \\
&= \frac{1}{2} \log(2 \pi) + \frac{1}{2} (\mu ^2 + \sigma^2)

\end{align*}
$$

Note that integral over a PDF is always 1 $$\int q(z) dz &= 1$$.

And, expecttaion over square of a random variable is equivalent to sum of square of mean and variance $$\int z^2 q(z) dz = \mu^2 + \sigma^2$$.

**Cross Entropy - Entropy**

Now let's put both the terms together:

$$
\begin{align*}

KL(q || p) &= \frac{1}{2} \log(2 \pi) + \frac{1}{2} (\mu ^2 + \sigma^2) - \frac{1}{2} \log(2 \pi) - \frac{1}{2} \log(\sigma^2)- \frac{1}{2} \\
&= - \frac{1}{2} (1 + \log(\sigma^2) + - \mu^2 - \sigma^2)

\end{align*}
$$

By stretch of the imagination, the above equation could be generalized to multivariate cases (D dimensions) by summing over all the dimensions:

$$
\begin{align*}

KL(q || p) &= - \frac{1}{2} \sum_{d=1}^{D} (1 + \log(\sigma^2) + - \mu^2 - \sigma^2)

\end{align*}
$$

The above equation can be easily implemented in frameworks like Pytorch. I hope the post helped you understand this concept a little better!

##References:

1: Auto-Encoding Variational Bayes by Kingma and Welling.
2: KL-divergence as an objective function by Tim Vieira.
3: Allison Chaney for the post image.