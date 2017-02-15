# Spike and Slab model 

Python code for my masters project. I've got a dataset of peptoids (a peptide mimicking molecule), and how effective they are against a range of different 
microbes. I'm using linear regression with sparsity-inducing priors (spike and slab ones to be precise) to figure out which features of the peptoids make them
good drugs. We want to use information from the microbes where we have a lot of data to help prediction in microbes where we don't have much data. And we want
an overall 'relevance score' for the features that's independent of a particular microbe. One way to get those things is to use 'Spike and slab' priors. 

A more technical/complete description can be found in these papers by Hernández-Lobato et. al, but I'll give a basic intro. Let's start with linear regression:

![Five Adam runs](https://github.com/AsaCooperStickland/Spike_And_Slab/blob/master/figures/linreg.gif)

y is a vector with our target values in, and X is a matrix with the features associated with each target value. Epsilon is Gaussian noise we add on to data point. 
In a Bayesian setting we put a prior on the weights w. The simplest prior is just Gaussian, but 
I'm doing something a bit (okay, maybe a lot!) more complicated, our prior looks like this: 

![Five Adam runs](https://github.com/AsaCooperStickland/Spike_And_Slab/blob/master/figures/s_n_s.gif)

We've introduced latent variables z. These zs are either 1, in which case we revert back to a Gaussian prior, or 0, in which case our prior is a 'Dirac delta'
centred on 0, in other words we have 100% probability that those weights are zero, and our model totally ignores them. This is another way of acheiving sparsity, 
with the most famous way being L1 regularisation. A nice thing about the spike and slab prior, other than it being fully Bayesian and giving us access to a 
posterior, is that thet posteriror of z gives a probabiltiy that a certain feature is relevant. This is very useful for my project, because I want to figure out
which features make a difference to drug effectiveness! 

We also have a prior on z which looks like: 

![Five Adam runs](https://github.com/AsaCooperStickland/Spike_And_Slab/blob/master/figures/zs.gif)

I.e. a Bernouilli probability distribution. p0 is the a priori proportion of features we think are relevant, so p0 = 0.5 means we think about half of the 
features will end up being used. The posterior of p0 gives us how many features are model thinks are relevant. 

Notice back in the prior on the weights we had vectors in the dirac delta and gaussian distributions, not scalars. This is because we have 'group sparsity'. 
We group together the weights for different microbes, for example charge for E.coli and charge for some other bacteria would be jointly zero or Gaussian. 
In this way we can 'share information' between microbes. If one microbe has loads of data points and it's obvious a certain feature isn't useful for prediction, 
that feature won't get used on the other microbes too. And the posterior for z will give us the probability that a certain feature is relevant across every 
microbe, not just one. 

This fancy prior means our posterior distribution is completely incractable, so we have to use MCMC (if you aren't familiar, this is a great intro by Iain
Murray of Edinburgh Uni.), specifically Gibbs sampling, since the conditional distributions of our variables are tractable. Details are in the appendix of 
this paper. 

# Toy Dataset

Okay, enough maths! We can demonstrate the principal using a toy dataset. 



