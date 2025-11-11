---
layout: distill
title: "Lecture 17"
description: "Generative Adversarial Networks (GANs)"
lecturers:
  - "Ben Lengerich"
authors:
  - "Ernie Dippold"
  - "Aashit Paliwal"
  - "Pranshu Dewagan"
editors:
  -
date: 2025-11-03
permalink: /notes/lecture-17/
---

# STAT 453: Scribe Notes

## November 3 — Generative Adversarial Networks (GANs)

### Topics

1. Review: Autoencoders
2. Generative Adversarial Networks (GANs)
3. GANs and VAEs: A Unified View

---

## 1. Autoencoders (Review)

**Goal:** Learn a compressed latent representation of input ( x ).

**Structure:**
$
\hat{x} = f(h) = f(g(x))
$
where:

* ( $g$ ): encoder
* ( $f$ ): decoder

### Variants

#### Denoising Autoencoders

* Add noise (e.g., dropout or Gaussian noise) to input.
* Train to reconstruct the original, uncorrupted input.
* Purpose: Learn robust representations that can remove noise.

#### Autoencoders with Dropout

* Dropout layers encourage redundancy in learned features.
* Improves generalization and robustness to missing inputs.

#### Sparse Autoencoders

Loss function:
$
L = \lVert x - \hat{x} \rVert^2 + \lambda \sum_i |h_i|
$

* Adds an L1 penalty on activations to enforce sparsity.
* Produces interpretable features — each neuron learns a distinct factor.

#### Variational Autoencoders (VAEs)

* Latent variable ( $z \sim \mathcal{N}(0, I)$ )
* Enables sampling new data points.
* Provides a probabilistic framework for generative modeling.

---

## 2. Generative Adversarial Networks (GANs)

**Definition:** Framework for training generative models through an adversarial process involving two neural networks:

* **Generator (G):** Learns to capture the data distribution ( $p_{data}(x)$ ) and generate samples that resemble real data.
* **Discriminator (D):** Learns to distinguish between real samples and generated samples.

### Objective Function

The two networks play a minimax game:

$
\min_G \max_D ; \mathbb{E}_{x \sim p_{data}(x)} [\log D(x)] + \mathbb{E}_{z \sim p_z(z)} [\log(1 - D(G(z)))]
$

At equilibrium:

* The generator perfectly replicates the data distribution.
* The discriminator outputs ( $\frac{1}{2}$ ) for all inputs (cannot distinguish real vs. fake).

**Properties:**

* Trained end-to-end via backpropagation.
* No need for Markov chains or explicit inference.

### Applications

* Image synthesis
* Text-to-image generation (e.g., diffusion models)
* Audio or text generation

---

## 3. GAN Training

### Discriminator Update

* Perform **gradient ascent**.
* Goal: Maximize probability of correct classification.

  * High probability on real samples.
  * Low probability on generated samples.

### Generator Update

* Perform **gradient descent** to fool the discriminator.

### Issues in Training

* **Oscillation** between generator and discriminator loss.
* **Mode collapse:** Generator produces limited types of samples.
* **Vanishing gradients:** Discriminator becomes too strong, leaving G unable to improve.

### Stopping Criterion

Training continues until **Nash equilibrium** — discriminator can no longer tell real from fake samples.

---

## 4. GANs and VAEs: A Unified View

| Feature         | Autoencoders (AEs)           | Variational Autoencoders (VAEs)  | GANs                         |
| --------------- | ---------------------------- | -------------------------------- | ---------------------------- |
| Goal            | Learn latent representations | Probabilistic generative model   | Adversarial generative model |
| Latent Variable | Deterministic ( $h = g(x)$ )   | ( $z \sim \mathcal{N}(0, I)$ )     | ( $z \sim p_z(z)$ )            |
| Training        | Reconstruction loss          | ELBO (KL + reconstruction)       | Adversarial minimax loss     |
| Sampling        | Deterministic decode         | Random sampling via latent prior | Generator sampling ( G(z) )  |
| Weakness        | Not generative               | Blurry outputs                   | Instability in training      |

**Common Ground:**

* All learn a latent-to-data mapping ( $z \to x$ ).
* GANs differ by generating full outputs at once (non-sequentially).
