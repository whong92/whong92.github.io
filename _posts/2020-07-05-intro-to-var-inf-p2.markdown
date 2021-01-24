---
layout: post_math
title:  "Intro to Variational Inference - Part 2"
date:   2020-07-04 19:52:26 +0100
author_profile: true
toc: true
classes: wide
categories: math 
---

In this part of the intro to variational inference (VI) series, we explore maximizing an intractable likelihood function $$p(X)$$ 
using the VI framework. First, we briefly touch on its connections to the EM-algorithm, and show how  EM is a special 
case of VI, and how they both use the variational lower bound (VLB). Then we generalize this idea by introducing the 
idea of variational posteriors, which will help with intractable EM problems. Finally we look at a popular and elegant 
approximation framework in VI - the mean field approximation framework, and how to use it in the VI framework.

## EM algorithm - Intuition

To motivate our discussion of the EM algorithm and Variational Inference, we consider models that correspond to the 
following probabilistic graphical model (PGM).

![](https://i.imgur.com/gzeHBNt.png)
*Simple latent variable model*

The EM algorithm is a general technique to find maximum likelihood solutions for probabilitistic models of this form. 
(
[(Dempster 1977)](https://rss.onlinelibrary.wiley.com/doi/10.1111/j.2517-6161.1977.tb01600.x), 
[(McLachlan 1997)](https://www.econstor.eu/bitstream/10419/22198/1/24_tk_gm_skn.pdf)
)

Many classes of problems can be mapped onto a PGM of this form, by simply lumping all latent variables into the 
collective variable $$\boldsymbol{Z}$$, and all parameters into $$\boldsymbol{\theta}$$. A caveat of doing this is that 
it may make some of our desired quantities intractable to compute, so we will restrict our discussion of EM to tractable 
models. Still, the EM algorithm that actually take us a long way to solving some rather nontrivial problems, optimally.

Our main aim here is to be maximize the log-likelihood of the above latent variable model $$\ln p(\boldsymbol{X} \vert \boldsymbol{\theta})$$
Which can be written in terms of $$\boldsymbol{Z}$$:

$$
\begin{align*}
\ln p(\boldsymbol{X} \vert \boldsymbol{\theta}) = \ln \left[  \sum_{\boldsymbol{Z}} p(\boldsymbol{X} , \boldsymbol{Z} \vert  \boldsymbol{\theta})\right]
\end{align*}
$$

There's a problem with being able to maximize this quantity with standard analytical techniques: the summation appearing 
inside the logarithm. Even if  $$p(\boldsymbol{X} , \boldsymbol{Z} \vert  \boldsymbol{\theta})$$ is from the exponential
family, the resulting log-marginal will typically not be as a result of the summation. This results in an expression that
may be intractable to maximize analytically.

Now imagine if for each observation $$\boldsymbol{X}$$, we were told its underlying latent variable $$\boldsymbol{Z}$$,
assuming the joint likelihood $$\ln p(\boldsymbol{X} , \boldsymbol{Z} \vert  \boldsymbol{\theta})$$ is 
easy to evaluate and maximize, with respect to $$\boldsymbol{\theta}$$. The pair $$\{\boldsymbol{X} , \boldsymbol{Z}\}$$
 and its likelihood function are referred to as the complete data set and the complete data likelihood function, respectively.

In practice however, we won't know $$\boldsymbol{Z}$$, so maximizing the complete data likelihood is not possible. The next
best thing we can work with is the posterior of the latent variables instead $$p(\boldsymbol{Z} \vert \boldsymbol{X}, \boldsymbol{\theta})$$,
which measures our *belief* in the distribution of $$\boldsymbol{Z}$$. We can use this to instead take an expectation of
the complete data likelihood under the posterior of $$\boldsymbol{Z}$$, like so:

$$
\begin{align}\label{eq:expectation_complete_data_likelihood}
\mathcal{Q}(\boldsymbol{\theta}, \boldsymbol{\theta}^{old}) = \sum_{\boldsymbol{Z}} p(\boldsymbol{Z} \vert \boldsymbol{X}, \boldsymbol{\theta}^{old}) \ln p(\boldsymbol{X} , \boldsymbol{Z} \vert  \boldsymbol{\theta})
\end{align}
$$

and maximize the expression with respect to $$\boldsymbol{\theta}$$, where we have held the values of $$\boldsymbol{\theta}$$
in the posterior fixed to $$\boldsymbol{\theta}^{old}$$. Not only does using the old values of the parameters in the 
posterior make the maximization easier since it no longer depends on $$\boldsymbol{\theta}$$, but it also has a 
mathematical justification as we explore the derivation of the VLB and how it gives rise to EM.

The EM algorithm then alternates between these two steps. In the E step, we compute the posterior $$p(\boldsymbol{Z} \vert \boldsymbol{X}, \boldsymbol{\theta}^{old})$$
using the parameters $$\boldsymbol{\theta}^{old}$$ from the previous iteration. In the M step, we maximize the expectation
$$(\ref{eq:expectation_complete_data_likelihood})$$, with respect to $$\boldsymbol{\theta}$$.

![](https://i.imgur.com/7jupFqs.jpg)
*Excellent visualization of the EM algorithm (adapted from ([Bishop 2006](https://www.springer.com/gp/book/9780387310732)))*
*showing each stage of the EM algorithm iteration. Frist, on the left, pre-E-step, the KL divergence*
*between $$q$$ and $$p$$ is non-zero, and there is a mismatch between the VLB and the log likelihood.*
*Post E-step (middle), $$q$$ is exactly equal to the posterior at the current parameter set $$\boldsymbol{\theta}$$,*
*and the VLB is equal to the log-lokelihood. Post M-step (right), is when the VLB is maximized w.r.t $$\boldsymbol{\theta}$$,*
*this causes the VLB to increase, and the log-likelihood to increase even more, since the KL divergence $$KL(q \vert p)$$ is*
*no longer zero with the new parameters.*

## EM and the VLB

Having build our intuition of how the algorithm works, we can give it a more formal treatment, and find out why our 
choice for the taking expectation over the posterior is justified.

We begin our discussion by introducing a distribution $$q(\boldsymbol{Z})$$ over our latent variables. We can show that
for any choice of $$q(\boldsymbol{Z})$$, the following decomposition holds (see derivation in appendix):

$$
\begin{align} \label{eq:VLB_decomp}
\ln p(\boldsymbol{X} \vert \boldsymbol{\theta}) = \mathcal{L}(q, \boldsymbol{\theta}) + \text{KL}(q \vert p)
\end{align}
$$

Where we have defined:

$$
\mathcal{L}(q, \boldsymbol{\theta}) = \sum_{\boldsymbol{Z}} q(\boldsymbol{Z}) \ln \left[ \frac{p(\boldsymbol{X}, \boldsymbol{Z} \vert \boldsymbol{\theta})}{q(\boldsymbol{Z})} \right]
$$

$$ 
\text{KL}(q \vert p) = - \sum_{\boldsymbol{Z}} q(\boldsymbol{Z}) \ln \left[ \frac{p(\boldsymbol{Z} \vert \boldsymbol{X}, \boldsymbol{\theta})}{q(\boldsymbol{Z})} \right]
$$

$$\mathcal{L}(q, \boldsymbol{\theta})$$ is a functional and is commonly referred to as the VLB, for reasons which will 
become obvious very soon, and $$\text{KL}(q \vert p)$$ is the KL divergence between the distributions $$q$$ and $$p$$.

It is well known that the KL-divergence satisfies $$\text{KL}(q \vert p) \ge 0$$, with strict equality when $$q=p$$. 
This implies that $$\mathcal{L}(q, \boldsymbol{\theta}) \le \ln p(\boldsymbol{X} \vert \boldsymbol{\theta})$$.
Therefore, $$\mathcal{L}(q, \boldsymbol{\theta})$$ is actually a **lower bound** of the likelihood function $$\ln p(\boldsymbol{X} \vert \boldsymbol{\theta})$$.

One more crucial observation is, since the likelihood function $$\ln p(\boldsymbol{X} \vert \boldsymbol{\theta})$$ does 
not depend on $$q$$ (for constant $$\boldsymbol{\theta}$$), the VLB $$\mathcal{L}(q, \boldsymbol{\theta})$$ attains its 
maximum value when $$q(\boldsymbol{Z})$$ is exactly equal to the posterior distribution $$p(\boldsymbol{Z} \vert \boldsymbol{X}, \boldsymbol{\theta})$$.
Furthermore, at the current (fixed) $$\boldsymbol{\theta}$$, given $$q=p$$, the VLB is exactly equal to the log likelihood function.

This observation is key to the EM algorithm. In the E-step, 
the parameters are held constant to $$\boldsymbol{\theta}^{old}$$, and $$\mathcal{L}(q, \boldsymbol{\theta}^{old})$$ is 
maximized with respect to $$q$$, by setting $$q=p$$.

In the subsequent M-step, $$q$$ is held constant, and the VLB is maximized with respect to $$\boldsymbol{\theta}$$, giving
a new updated set of parameters $$\boldsymbol{\theta}^{new}$$. This also guarantees that the value of the log-likelihood has
increased (unless it is already at a maximum) since:

$$
\ln p(\boldsymbol{X} \vert \boldsymbol{\theta}^{new}) \ge \mathcal{L}(q, \boldsymbol{\theta}^{new}) \ge \mathcal{L}(q, \boldsymbol{\theta}^{old}) = \ln p(\boldsymbol{X} \vert \boldsymbol{\theta}^{old})
$$

Where $$q(\boldsymbol{Z})$$ is the posterior $$p(\boldsymbol{Z} \vert \boldsymbol{X}, \boldsymbol{\theta}^{old})$$ with
the old paramater set. Another way of looking at this is that since $$q(\boldsymbol{Z})$$ is held at the old posterior,
it is no longer equal to the posterior at the new point in parameter space $$p(\boldsymbol{Z} \vert \boldsymbol{X}, \boldsymbol{\theta}^{new})$$,
and so the $$\text{KL}$$ divergence has neccessarily increased. This causes the log-likelihood to increase more than the
VLB has increased.

How do we get to $$(\ref{eq:expectation_complete_data_likelihood})$$ from the above analysis? Well, if we look at 
$$\mathcal{L}(q, \boldsymbol{\theta})$$, we can break it down into the following two terms:

$$
\begin{align*}
\mathcal{L}(q, \boldsymbol{\theta}) & = \sum_{\boldsymbol{Z}} q(\boldsymbol{Z}) \ln p(\boldsymbol{X} , \boldsymbol{Z} \vert  \boldsymbol{\theta})
 + \sum_{\boldsymbol{Z}} q(\boldsymbol{Z}) \ln q(\boldsymbol{Z}) \\
& = \sum_{\boldsymbol{Z}} q(\boldsymbol{Z}) \ln p(\boldsymbol{X} , \boldsymbol{Z} \vert  \boldsymbol{\theta})
 + \text{const} \\
\end{align*}
$$
   
where we have noted that the 2nd term of the RHS is constant with resepect to $$\boldsymbol{\theta}$$.

With $$q(\boldsymbol{Z})=p(\boldsymbol{Z} \vert \boldsymbol{X}, \boldsymbol{\theta}^{old})$$, $$\mathcal{L}(q, \boldsymbol{\theta})$$ 
is exactly equal to the quantity $$\mathcal{Q}(\boldsymbol{\theta}, \boldsymbol{\theta}^{old})$$ in $$(\ref{eq:expectation_complete_data_likelihood})$$.
Therefore maximizing $$(\ref{eq:expectation_complete_data_likelihood})$$ w.r.t $$\boldsymbol{\theta}$$ is equivalent to 
maximizing the VLB.

## Making EM more tractable - Variational Posteriors

Having seen the rationale and the derivation behind EM, we can now use our knowledge in variational
inference. We can see that the EM algorithm requires that we have a tractable expression of the posterior of the latent variables
$$p(\boldsymbol{Z} \vert \boldsymbol{X})$$. It is not always possible to obtain a tractable form of 
this distribution, especially if the latent variables have very different priors but are coupled together, giving rise
to complicated expressions for the exact posterior. The situation is made more difficult when we realize we also have to
compute the expectation $$(\ref{eq:expectation_complete_data_likelihood})$$.

Recall the decomposition of the log-likelihood function:

$$
\begin{align} \label{eq:VLB_decomp2}
\ln p(\boldsymbol{X}) = \mathcal{L}(q) + \text{KL}(q \vert p)
\end{align}
$$

we can see that if we allowed $$q(\boldsymbol{Z})$$ to be *any* distribution, the KL divergence vanishes when $$q(\boldsymbol{Z})$$
is exactly equal to $$p(\boldsymbol{Z} \vert \boldsymbol{X})$$. In order to fix this, we can restrict the family of 
distributions $$q(\boldsymbol{Z})$$, so that the maximization of the lower bound (and by proxy the log-likelihood) is 
tractable.

One way to do this is to parametrize the distribution $$q(\boldsymbol{Z} \vert \omega)$$, and use standard nonlinear 
optimization techniques to maximize the lower bound. This is precisely what variational auto-encoders ([(Kingma & Welling 2013)](https://arxiv.org/abs/1312.6114)) 
try to do, but that is out of the scope of this article. 

Another way to achieve tractability is to assume *a-priori* an algebraic form for $$q(\boldsymbol{Z})$$, and use variational
calculus to maximize the VLB which is a functional of the distribution $$q$$. One such restriction is to assume $$q(\boldsymbol{Z})$$
can be partitioned into groups $$\boldsymbol{Z} = \{\boldsymbol{Z}_0, \boldsymbol{Z}_1, ... ,\boldsymbol{Z}_M\}$$, such
that the distribution $$q(\boldsymbol{Z})$$ factorizes over these groups.

$$
\begin{align}\label{eq:factorized_dist}
q(\boldsymbol{Z}) = \prod_{i=1}^M q_i(\boldsymbol{Z}_i)
\end{align}
$$

This is commonly known as the mean field approximation (MFA) ([(Jordan 1999)](https://people.eecs.berkeley.edu/~jordan/papers/variational-intro.pdf)). 

## The Mean Field Approximation

In MFA, we seek the disribution $$q(\boldsymbol{Z})$$ over which the value of the lower bound $$\mathcal{L}(q)$$ is greatest,
and we will be trying to do this via a variational optimization of the distribution $$q$$, where $$q$$ is of the form ($$\ref{eq:factorized_dist}$$).

Recall in part 1, we defined the idea of a functional derivative, and finding the stationary point of the functional $$F(y) = \int G(y, y', x) dx$$
with respect to the function being optimized, gave us the Euler-Lagrange equation:

$$ 
\frac{\partial{G}}{\partial y} + \frac{d}{dx} \left [ \frac{\partial G}{\partial y'}\right] = 0
$$

Where $$G$$ is a function of both $$y$$ and $$y'$$, and $$y$$ is a function defined over $$x$$. For maximizing $$\mathcal{L}(q)$$
w.r.t $$q$$, we can drop the second term since $$q'$$ does not appear in $$\mathcal{L}(q)$$, and simply solve for:

$$
\begin{equation}\label{euler-lagrange}
\frac{\partial J(q)}{\partial q} = 0 \quad \text{where} \quad \mathcal{L}(q) = \int J(q(\boldsymbol{Z})) d \boldsymbol{Z}
\end{equation}
$$

Substituting $$(\ref{eq:factorized_dist})$$ into the definition of the VLB, and dropping $$\boldsymbol{\theta}$$ to keep the
notation uncluttered, we get:

$$
\mathcal{L}(q) = \int \prod_{i=1}^M q_i(\boldsymbol{Z}_i) \ln \left( p(\boldsymbol{X}, 
\boldsymbol{Z}) - \sum_i^M \ln q_i(\boldsymbol{Z}_i) \right) d \boldsymbol{Z}
$$

We have also replaced the summation here with an integral over continuous latent variables, but the analysis that follows
should also apply just as well for discrete latent variables.

### Method 1 - Direct Maximization of the VLB

Using $$(\ref{euler-lagrange})$$, we seek to minimize $$\mathcal{L}(q)$$ with respect to one component in the factorized 
distribution $$q_j$$. We also have to add an additional term (a Lagrangian) to the functional to be maximized, to impose 
the following constraint, so that $$q_j$$
is a valid distribution.

$$
\int q_j(\boldsymbol{Z}_j) d\boldsymbol{Z}_j = 1 
$$

This means we need to maximize the Lagrangian $$L$$ w.r.t $$q_j$$:

$$
\begin{align*}
L & = \mathcal{L}(q) + \lambda \int q_j(\boldsymbol{Z}_j) d\boldsymbol{Z}_j \\
& = \int {J}(q_j) d\boldsymbol{Z}_j + \lambda q_j(\boldsymbol{Z}_j) d\boldsymbol{Z}_j \\
\end{align*}
$$

Where $$ \mathcal{L}(q) = \int {J}(q_j) d\boldsymbol{Z}_j $$. Applying Euler-Lagrange to the Lagrangian $$L$$, we seek to solve the following equation to obtain $$q_j$$:

$$
\begin{align} \label{eq:el_lagrangian}
 \frac{\partial}{\partial q_j} \left[ J(q_j) + \lambda  \left(q_j(\boldsymbol{Z}_j)- 1 \right) \right] = 0
\end{align}
$$

We can make our lives a little easier by isolating terms in $$\mathcal{L}(q)$$ that do not include $$q_j$$:

$$
\begin{align*}
 \mathcal{L}(q) & = \int q_j \int \prod_{i \neq j}^M q_i \ln \left( p(\boldsymbol{X}, 
\boldsymbol{Z}) \right) d\boldsymbol{Z}_{i \neq j} d\boldsymbol{Z}_j
- \int \prod_{i}^M q_i \sum_i^M \ln q_i d \boldsymbol{Z} \\

& = \int q_j \int \prod_{i \neq j}^M q_i \ln \left( p(\boldsymbol{X}, 
\boldsymbol{Z}) \right) d\boldsymbol{Z}_{i \neq j} d\boldsymbol{Z}_j
- \int \prod_i^M q_i \ln q_j d \boldsymbol{Z} 
- \int \prod_i^M q_i \sum_{i \neq j} \ln q_j d \boldsymbol{Z}\\

& = \int q_j \int \prod_{i \neq j}^M q_i \ln \left( p(\boldsymbol{X}, 
\boldsymbol{Z}) \right) d\boldsymbol{Z}_{i \neq j} d\boldsymbol{Z}_j
- \int q_j \ln q_j d \boldsymbol{Z}_j + K_1 \\

& = \int q_j \left \{ \mathbb{E}_{i \neq j} \left[ \ln \left( p(\boldsymbol{X}, 
\boldsymbol{Z}) \right) \right] + \ln q_j \right \}  d \boldsymbol{Z}_j 
+ K_1  + K_2 \\

\end{align*}
$$

Giving us the simplified form of the VLB:

$$
\begin{equation} \label{eq:VLB_simplified}
\mathcal{L}(q) = \int q_j \left \{ \mathbb{E}_{i \neq j} \left[ \ln \left( p(\boldsymbol{X}, 
\boldsymbol{Z}) \right) \right] + \ln q_j \right \}  d \boldsymbol{Z}_j 
+ K \\
\end{equation}
$$

Where $$K = K_1 + K_2$$ is a constant w.r.t $$q_j$$. Applying $$(\ref{eq:el_lagrangian})$$ to the simplified expression
of $$\mathcal{L}(q)$$, and noting that 

$$ J(q_j) = \mathbb{E}_{i \neq j} \left[ \ln \left( p(\boldsymbol{X}, 
\boldsymbol{Z}) \right) \right] d\boldsymbol{Z}_j + \ln q_j $$

we get:

$$
\begin{align*}
\mathbb{E}_{i \neq j} \left[ \ln \left( p(\boldsymbol{X}, 
\boldsymbol{Z}) \right) \right] - \ln q_j + 1 + K + \lambda = 0 \\
\mathbb{E}_{i \neq j} \left[ \ln \left( p(\boldsymbol{X}, 
\boldsymbol{Z}) \right) \right] - \ln q_j + K + \lambda = 0
\end{align*}
$$

Giving us an expression of optimal $$q_j$$:

$$
\begin{align*}
q_j &= \exp \left( \mathbb{E}_{i \neq j} \left[ \ln \left( p(\boldsymbol{X}, 
\boldsymbol{Z}) \right) \right] \right)  e^{K+\lambda}
\end{align*}
$$

To solve for $$\lambda$$, we differentiate the Lagrangian w.r.t $$\lambda$$, we simply get back the normalization condition:

$$
\int q_j(\boldsymbol{Z}_j) d\boldsymbol{Z}_j = 1
$$

This implies that the multiplicative constant $$e^{K+\lambda}$$ is a normalization constant:

$$
\int q_j d\boldsymbol{Z}_j  =  e^{K+\lambda} \int q_j \exp \left( \mathbb{E}_{i \neq j} \left[ \ln \left( p(\boldsymbol{X}, 
\boldsymbol{Z}) \right) \right] \right)  d\boldsymbol{Z}_j  = 1
$$

and so, 

$$
e^{K+\lambda} = \frac{1}{\int q_j \exp \left( \mathbb{E}_{i \neq j} \left[ \ln \left( p(\boldsymbol{X}, 
\boldsymbol{Z}) \right) \right] \right)  d\boldsymbol{Z}_j}
$$

Giving us the final expression of $$q_j$$

$$
\begin{align} \label{eq:q_j_final_form}
q_j =  \frac{\exp \left( \mathbb{E}_{i \neq j} \left[ \ln \left( p(\boldsymbol{X}, 
\boldsymbol{Z}) \right) \right] \right) }{

\int
\exp \left( \mathbb{E}_{i \neq j} \left[ \ln \left( p(\boldsymbol{X}, 
\boldsymbol{Z}) \right) \right] \right) d \boldsymbol{Z}_j

}
\end{align}
$$


### Method 2 - Minimizing KL Divergence

Another method to arrive at ($$\ref{eq:q_j_final_form}$$), is by noting that the VLB (from ($$\ref{eq:VLB_simplified}$$)) 
can be written as:

$$
\begin{align*}
\mathcal{L}(q) &= \int q_j \left \{ \mathbb{E}_{i \neq j} \left[ \ln \left( p(\boldsymbol{X}, 
\boldsymbol{Z}) \right) \right] + \ln q_j \right \}  d \boldsymbol{Z}_j 
+ K \\

&= \int q_j \left \{ \ln \left( \exp \left( \mathbb{E}_{i \neq j} \left[ \ln \left( p(\boldsymbol{X}, 
\boldsymbol{Z}) \right) \right] \right) \right)   + \ln q_j \right \}  d \boldsymbol{Z}_j 
+ K \\

&= \int q_j \ln \left( \frac{  \exp \left( \mathbb{E}_{i \neq j} \left[ \ln \left( p(\boldsymbol{X}, 
\boldsymbol{Z}) \right) \right]  \right) / U } { q_j } \right)   d \boldsymbol{Z}_j 
+ \text{const} \\

\end{align*}
$$

Where $$U$$ is some normalization constant, to make the expression in the numerator in the $$\ln$$ a proper distribution. 
We can recognize that $$\mathcal{L}(q)$$ is actually the KL-divergence (plus some constant term) between $$q_j$$ and the 
distribution

$$
 \exp \left( \mathbb{E}_{i \neq j} \left[ \ln \left( p(\boldsymbol{X}, 
\boldsymbol{Z}) \right) \right]  \right) / U
$$

And since the KL-divergence attains its maximum value ($$0$$) when the numerator in the $$\ln$$ equals the denominator,
the solution to $$q_j$$ that maximizes the VLB must be

$$
\begin{align*}
 q_j &= \frac{ \exp \left( \mathbb{E}_{i \neq j} \left[ \ln \left( p(\boldsymbol{X}, 
\boldsymbol{Z}) \right) \right]  \right) }{ U } \\
&= \frac{\exp \left( \mathbb{E}_{i \neq j} \left[ \ln \left( p(\boldsymbol{X}, 
\boldsymbol{Z}) \right) \right] \right) }{

\int
\exp \left( \mathbb{E}_{i \neq j} \left[ \ln \left( p(\boldsymbol{X}, 
\boldsymbol{Z}) \right) \right] \right) d \boldsymbol{Z}_j

}
\end{align*}
$$

## Conclusion

In this post, we learned about the EM algorithm, which plays a central part in variational inference. We learned that
using EM in its original form requires us to be able to evaluate the posterior on the latent variables given the 
observed variables, and that is might not always be possible.

Variational methods give us a way out - we need not always exactly maximize the VLB in the E-step of the EM algorithm,
but instead we can maximize this with respect to a restricted family of distributions, that make the evaluation of the
posterior more tractable. We do this using the tools of variational optimization.

Finally, we looked at one particular family of restricted distributions - factorized distributions. Another term for 
this type of approximation is the mean field approximation (MFA), this is so named because the resulting expressions for
the posterior is actually an expectation of the other factors, and thus we 'average'(or take a mean) over the other 
fields (latent variables). 

In the next post, we can look at particular examples that use the MFA.

## Appendix

### Derivation of VLB decomposition 

The following holds for any valid distribution $$q(\boldsymbol{Z})$$ over the latent variables $$\boldsymbol{Z}$$

$$
\ln p(\boldsymbol{X}) = \sum_{\boldsymbol{Z}} q(\boldsymbol{Z}) \ln p(\boldsymbol{X}) 
$$

$$
\begin{align*}
 \ln p(\boldsymbol{X}) &= \sum_{\boldsymbol{Z}} q(\boldsymbol{Z}) \ln 
\frac{p(\boldsymbol{X}, \boldsymbol{Z}) } { p(\boldsymbol{Z} \vert \boldsymbol{X}) } 
\frac{q(\boldsymbol{Z})}{q(\boldsymbol{Z})} \\

&= \sum_{\boldsymbol{Z}} q(\boldsymbol{Z}) \ln 
\frac{p(\boldsymbol{X}, \boldsymbol{Z}) } {q(\boldsymbol{Z})} 
\frac{q(\boldsymbol{Z})} { p(\boldsymbol{Z} \vert \boldsymbol{X}) }  \\


&= \sum_{\boldsymbol{Z}} q(\boldsymbol{Z}) \left \{ \ln 
\frac{p(\boldsymbol{X}, \boldsymbol{Z}) } {q(\boldsymbol{Z})} 
 -  \ln \frac{q(\boldsymbol{Z})} { p(\boldsymbol{Z} \vert \boldsymbol{X}) }  \right \} \\
\end{align*}
$$

## References 

[Maximum Likelihood from Incomplete Data Via the EM Algorithm (Dempster 1977)](https://rss.onlinelibrary.wiley.com/doi/10.1111/j.2517-6161.1977.tb01600.x)

[The EM algorithm (McLachlan 1997)](https://www.econstor.eu/bitstream/10419/22198/1/24_tk_gm_skn.pdf)

[Auto-Encoding Variational Bayes (Kingma & Welling 2013)](https://arxiv.org/abs/1312.6114)

[An Introduction to Variational Methods
for Graphical Models (Jordan 1999)](https://people.eecs.berkeley.edu/~jordan/papers/variational-intro.pdf)

[Pattern Recognition and Machine Learning (Bishop 2006)](https://www.springer.com/gp/book/9780387310732)