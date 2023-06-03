---
layout: extended-content
title: "Molecular orbitals without tears"
permalink: /motheory
usemathjax: true
---

__Last updated: May 2023__

1. [Preamble](#preamble)
2. [What is an atomic orbital?](#atomic_orbitals)
3. [What is a molecular orbital?](#molecular_orbitals)
4. [Some applications](#applications)

<a name="preamble"></a>

### Preamble

Modern teaching of physical

In this handout, I experiment with a different approach. Instead of
handwaving away the equations and the meaning of the problems

<a name="atomic_orbitals"></a>

### What is an atomic orbital?

The behaviour of electrons in atomic systems is governed by the
Schrödinger equation:

$$\hat{\mathcal{H}}\Psi_k(\mathbf{x}) = E_k\Psi_k(\mathbf{x})$$

The simplest version of this problem is the

$$-\frac{\hbar^2}{2m_e}\nabla^2\Psi_k(\mathbf{r}) - \frac{e^2}{4\pi\epsilon_0 r}\Psi_k(\mathbf{r}) = E_k\Psi_k(\mathbf{r})$$

The Schrodinger equation

The solutions for the hydrogen atom represent the end of the road for exact solutions of equations. Everything from here is approximations.

Here is the equation for the helium atom:

$$-\frac{\hbar^2}{2m_e}\nabla^2\Psi_k(\mathbf{r}_1, \mathbf{r}_2) - \frac{e^2}{4\pi\epsilon_0}\left[ \frac{1}{|\mathbf{r}_1|} + \frac{1}{|\mathbf{r}_2|} + \frac{1}{|\mathbf{r}_1 - \mathbf{r}_2|}\right]\Psi_k(\mathbf{r}_1, \mathbf{r}_2) 
= E_k\Psi_k(\mathbf{r}_1, \mathbf{r}_2)$$

There are reasons why it is not possible to solve equations that contain
the $$|\mathbf{r}_1-\mathbf{r}_2|^{-1}$$ term in the equation above. To get an intuition of why this might be hard.

The simplest way to solve the problem is to cast is as a *hydrogenoid*
atom. In other words, we assume a system that has only one electron,
subject. We talk of the *effective nuclear charge* $Z_\text{eff}$:

$$-\frac{{\hbar^2}}{{2m_e}}\nabla^2\Psi_k(\mathbf{r}) - \frac{e^2Z_\text{eff}}{{4\pi\epsilon_0 r}}\Psi_k(\mathbf{r}) = E_k\Psi_k(\mathbf{r})$$

There are multiple reasons why this approach could work. The helium
atom, after all, looks very much like the hydrogen atom. 

Discussing the effect of the effective nuclear charge occupies much of the discussion of periodic properties in elementary courses in general chemistry or physical chemistry, so I will not talk about it here.

<a name="molecular_orbitals"></a>

### What is a molecular orbital?

We have seen how in atomic systems we can describe the system by making
up a hydrogenoid system that captures most of the properties of an atom.
One immediate question to ask is whether we can do the same in molecular
systems. The answer turns out to be no because, unlike in the case of a
single atom, we now need to consider multiple nuclei. If you go back to the equations in the previous section, you will see
that we represent the atomic nucleus in the center of coordinates, and
describe the electron(s) as their distances to the center.
Mathematically, the Hamiltonian has the following form:

$$\hat{\mathcal{H}}\Psi_k(\mathbf{x}) = \Big[ - \frac{\hbar^2}{2m_e}\sum_i \nabla_i^2 - \sum_A\sum_i \frac{Z_A}{|\mathbf{r}_i-\mathbf{R}_A|} + \sum_i\sum_{j>i} \frac{1}{|\mathbf{r}_i-\mathbf{r}_j|} \Big] \Psi_k(\mathbf{x})$$

As usual, there are multiple ways to solve this problem.

The simplest approach we can think of is the so-called **linear
combination of atomic orbitals (LCAO)**, where we try to describe the
wavefunction of a molecule in terms of the combinations of the atomic
orbitals of the constituent atoms. Consider the molecule of hydrogen .
We can describe the wavefunction of in terms of the two atomic orbitals
of its constituent atoms:

$$\Psi(\mathbf{x}) = a\Psi_{1s, H(1)} + b\Psi_{1s, H(2)}$$

We can then find out the values of the coefficients $$a$$ and $$b$$ that
provide the best description of the wavefunction of the molecule, using
a clever scientific method known as the "variational principle" (for details,
see any quantum chemistry textbook, for example _Quantum Chemistry_ by
Ira N. Levine). For hydrogen, symmetry arguments show that it is a 

In more complex molecules, we can use the same approach. 

<a name="applications"></a>

### Some applications

#### Arguing about chemical bonding

#### $$\pi$$-$$\pi$$ interactions
