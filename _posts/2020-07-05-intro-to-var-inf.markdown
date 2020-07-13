---
layout: post_math
title:  "Intro to Variational Inference - Part 1"
date:   2020-07-04 19:52:26 +0100
author_profile: true
toc: true
classes: wide
categories: math 
---

This is the first of an N part series (I don't know N yet), where I take a deep dive into a class of techniques commonly known as variational inference. This is part of a broader class of approximate techniques in machine learning, where one seeks approximate solutions to what are usually intractiable optimization problems over probability distributions. 

In part 1, we first look at the grand daddy of variational inference - the calculus of variations. While understanding this framework is not neccessary for one to be able to master variational inference, it is definitely a great tool to build intuition and makes for a more rewarding journey. 

So here goes.

## Motivation 

In many classes of machine learning problems, the problem can be framed as one is interested in finding a probability distribution $$p(Z,X)$$ to explain some data $$X$$, where $$Z$$ is a latent factor governing $$X$$. In many cases, there is also a parameter set $$\boldsymbol{\theta}$$ governing the relationship between $$Z$$ and $$X$$.

\<Put a simple graphical model here.\>

A popular example is the Gaussian Mixture Model, which describes a mixture of $$k$$ gaussians, with means and variances $$\boldsymbol{\mu_k}, \boldsymbol{\Sigma_k}$$, and a multinomial with coefficients $$\pi_k$$, where:

$$
\sum_{k=1}^{K} \pi_k = 1
$$

The holy grail of many statisticians would indeed to be able to model the full distribution $$p(X,Z)$$ (or even $$p(X,Z,\boldsymbol{\theta})$$). This usually involves the maximization of the marginal log-likelihood of the data $$X$$:

$$
\log p(X) = \log \int p(X,Z) dZ
$$

In many cases this is infeasible to solve for exactly, possibly due to the high dimensionality of the latent factors $$Z$$, or the analytic intractibility of evaluating and maximizing $$\log \int{p(X,Z)}dZ$$.

For GMM's this is solved by utilizing the EM algorithm (insert link here), to approximately maximize $$p(X)$$, w.r.t to the gaussian and multinomial parameters, via an alternating maximization of a lower bound while holding the posterior $$p(Z\|X,\theta)$$ constant, and maximizing for $$\theta$$, and vice versa. This may be tractable for the GMM's but in general evaluating $$p(Z\|X,\theta)$$ may still be problematic as it still involves computing $$\int{p(X,Z)}dZ$$ in one form or another.

Variational Inference gives a solution by attemping to model such complicated distributions by 'fitting' it with a class of simpler distributions, in order to make computing the above quantities mathematically tractable.

$$
q(Z) \approx p(Z)
$$

## Calculus of variations

At the heart of variational inference is the calculus of variations. First formulated in the works of Leonhard Euler and Joseph Louis Lagrange. At the core of this idea is the concept of a **functional**. This is defined as a mapping that tales a function as an input and returns the value of the functional as an output. In mathematical terms, it maps functions $$f(x)$$ to a scalar.

$$
F: f \mapsto \mathbb{R}
$$

A simple example of a functional would be the definite integral of a function $$f(x)$$ over an interval $$[a,b]$$.

$$
F[f] = \int^b_a f(x) dx
$$

An example more relevant to what we are going for, would be the variational lower bound, w.r.t the distribution $$q(Z)$$

$$
\text{VLB}[q] = \int q(Z) \log \left[ \frac{p(X,Z)}{q(Z)} \right] dZ
$$

Which appears in the expression of the log marginal probability, and acts as a surrogate to maximize the marginal proability.

$$
\log p(X) = \text{VLB}[q] + \text{KL}(q\|p)
$$

An immediate follow up to this concept is the idea of a *functional derivative*. This idea extends the idea of a regular derivative of a point on a function, to the derivative of a function on its functional. To make clear this idea, lets consider a functional $$F[y]$$ perturbed by a random function $$\eta(x)$$ :

$$
\begin{equation} \label{bla}
 F[y(x) + \epsilon \eta(x)] = F[y(x)] + \epsilon \frac{dF}{d\epsilon}(\epsilon=0) + O(\epsilon^2)
\end{equation}
$$

If we are considering functionals of the general form:

$$
F[y] = \int G(y(x), y'(x), x) dx
$$

the derivative $$F_\epsilon$$ can always be written into the following form, in terms of $$\eta$$.

$$
\begin{equation} \label{func_deriv}
F_\epsilon = \int \frac{\delta F}{\delta y(x)} \eta(x) dx
\end{equation}
$$

Where we have defined the term $$\frac{\delta F}{\delta y(x)}$$ here to be the *functional derivative* of $$F$$. We don't know what this functional derivative may look like at this point in time, since we don't know what $$G$$ is, but what we find out is that the functional derivative can actually be written in terms of regular derivatives of $$G$$. Breaking down $$\ref{bla}$$ further, by taking the expansion of $$G(y(x)+\epsilon \eta(x))$$, we get:

$$
 F[y(x) + \epsilon \eta(x)] = F[y(x)] + \epsilon \int \left \{ \frac{\partial{G}}{\partial y} \eta(x) + \frac{\partial{G}}{\partial y'} \eta'(x) \right \} dx + O(\epsilon^2)
$$

Noting that the we can integrate the second term in the curly braced by parts, and the fact that $$\eta(x)$$ goes to zero at the boundaries of the integration (since $$y(x)$$ is fixed at the boundary), we get (derivation in [appendix](/math/intro-to-var-inf/#appendix)):

$$
\begin{equation} \label{bla2}
 F[y(x) + \epsilon \eta(x)] = F[y(x)] + \epsilon \int \left \{ \frac{\partial{G}}{\partial y} + \frac{d}{dx} \left [ \frac{\partial G}{\partial y'}\right] \right \}  \eta(x) dx + O(\epsilon^2)
\end{equation}
$$

Having successfully massaged $$\ref{bla2}$$ into the form $$\ref{bla}, \ref{func_deriv}$$, we can write, for functionals of the form $$G$$, we can write the functional derivative of $$F$$ as:

$$
\frac{\delta F}{\delta f(x)} = \frac{\partial{G}}{\partial y} + \frac{d}{dx} \left [ \frac{\partial G}{\partial y'}\right]
$$

Noting that we require $$F_\epsilon = 0$$ for **any** $$\eta(x)$$, implies that the functional derivative $$ \frac{\delta F}{\delta y(x)} $$ is required to be zero **everywhere**. 

We can now write:

$$
\begin{equation}\label{euler-lagrange}
  \frac{\partial{G}}{\partial y} + \frac{d}{dx} \left [ \frac{\partial G}{\partial y'}\right] = 0
\end{equation}
$$

Which is the famous Euler-Lagrange equations.

## Solving Euler-Lagrange

The equation $$ \ref{euler-lagrange} $$ is actually useful enough for us to derive some interesting results now. Take for example the functional that computes the arc length of a curve $$y(x)$$ between two points $$x=a$$ and $$x=b$$:

$$
\begin{align*}
F[y] && = \int_{x=a}^{x=b} \sqrt{dy^2 + dx^2} \\
&& = \int_{x=a}^{x=b} \sqrt{y'^2 + 1} dx
\end{align*}
$$

Invoking Euler-Lagrange, we can write:

$$
\begin{align*}
  \frac{d}{dx} \left[ \frac{\partial}{\partial y'} \sqrt{y'^2 + 1} \right] = 0 \\
  \frac{y'}{\sqrt{y'^2 + 1}} = C \\ 
  y' = \frac{C}{\sqrt{1-C^2}} = \text{const.}
\end{align*}
$$

Where $$C$$ is some constant of integration. What this tells us is that the shape of $$y(x)$$ that minimizes the arc length is a straight line.

I for one am a sucker for simple, universal truths that pop out of these very rich, sophisticated ideas. So this result is actually pretty miraculous to me.

## Next Time

In the next part of this series, we will look at how to use variational calculus to find approximate solutions to optimization problems involving probability distributions. This will usually involve funding a distribution $$q(X)$$ from a family of distributions to approximate some other distribution $$p(X)$$. Functionals that will be of interest to us include the variational lower bound (shown earlier), as well as the Kullback-Liebler divergence.

## Appendix
