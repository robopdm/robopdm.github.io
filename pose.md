---
layout: page
title: "Representations of Position, Orientation and Motion"
mathjax: true
permalink: /pose/
---

Table of Contents:

- [Introduction](#intro)
- [Conventions](#conv)
- [Trig Review](#trig)
- [Planar Rotations](#planar)
- [Orientation in 3D](#three-dim)
	- [Rotation Matrices](#rotm)
	- [Quaternions](#quater)
	- [Axis-Angles](#axisang)
- [Summary](#summary)

<a name='intro'></a>
## Introduction

> The heart of robotics is motion. (Matt Mason)

Robotics involves perceiving, predicting, creating and controlling motion. Motion is the change in position over time. Thus, we need to be able to represent the position and orientation of a rigid body in 3D space to be able to do these things.

<a name='conv'></a>
## Notation and Conventions

In this course, we'll use the following notation:

* $A \in \mathcal{R}^{m \times n}$: a capital letter will denote a matrix with m rows and n columns. The entries of $A$ will be real-valued.
* $x \in \mathcal{R}^{n}$: a small letter will denote a vector with n entries.
* $\hat{x}$: vectors with a hat will denote unit vectors, i.e. vectors of length 1.

All vectors are to be considered column vectors, i.e.

$$
\begin{align}
  x &= \begin{bmatrix}
          x_{1} \\
          x_{2} \\
          \vdots \\
          x_{n}
        \end{bmatrix}
\end{align}
$$

If we want to explicitly denote row vectors, we will use the notation $x^T = [x_1, \dots, x_n]$.

Because we work with column vectors, multiplications involving rotation and general manipulation of these vectors will be represented as pre-multiplications, e.g. $y = Rx$.

We'll also be using a right-hand coordinate system. That is the x, y, and z axes of our frames are aligned with the index finger, middle finger, and thumb of the right hand, respectively. Or mathematically, that $\widehat{x} \times \widehat{y} = +\widehat{z}$.

<div class="fig figcenter">
  <img src="/assets/right-hand-rule.png" style="width:200px;"/>
</div>

Positive rotation in a right-hand system is obtained using the "right-hand rule": by aligning the thumb with the axis of rotation in the positive direction, the curl of the fingers represents a positive motion from the first axis to the second axis. In a right-hand coordinate system, this implies that a positive rotation about the z-axis when viewed from above is counter-clockwise.

<!-- This also means that for any rotation matrix we consider, $\operatorname{det}R = +1$. -->

<a name='trig'></a>
## Trigonometry Review

In this section, we'll be reviewing a few trig properties as well as examples of how to derive them. The purpose isn't to cover them all, but to get you acclimated to the math for future sections.

A handy mnemonic to remember the cosine, sine and tangent relationships in a right triangle is "soh-cah-toa".

<div class="fig figcenter">
  <img src="/assets/sohcahtoa.png" style="width:250px;"/>
</div>

That is, $\sin\theta = \frac{\textbf{o}\text{pposite}}{\textbf{h}\text{ypotenuse}}$, $\cos\theta = \frac{\textbf{a}\text{djacent}}{\textbf{h}\text{ypotenuse}}$ and $\tan\theta = \frac{\textbf{o}\text{pposite}}{\textbf{a}\text{djacent}}$.

We can apply these definitions in a unit circle to derive useful properties of these functions.

<div class="fig figcenter">
  <img src="/assets/unit-circle.jpg" style="width:500px;"/>
</div>

In the figure above, let OP be a radius of the circle with $\angle POA = \theta$. From the definition of sine and cosine, we have that $\cos \theta = OA$ and  $\sin \theta = AP$ since $OP = 1$. Thus, the coordinates of the point P are $(\cos \theta, \sin \theta)$. Similarly, we can see that the point drawn at $-\theta$ has the same $x$ value as P but the negative of its $y$ value. This means that we can write an expression relating the cosine/sine of an angle with its negative:

$$
\cos (- \theta) = \cos \theta \\
\sin (- \theta) = - \sin \theta
$$

In fact, cosine is an even function and sine is an odd function. Next, consider the point Q, drawn with an angle of $\theta + 90$ from the origin. In the right triangle QPO, the angle Q is equal to $\theta$ and O equal to $90 - \theta$. This means that QBO and OAP are similar triangles. As such,

$$
\cos(90 - \theta) = OB = AP = \sin \theta \\
\sin(90 - \theta) = QB = OA = \cos \theta
$$

from which we can derive the expressions for $90 + \theta$:

$$
\cos(90 + \theta) = \cos(90 - (-\theta)) = \sin(-\theta) = - \sin \theta \\
\sin(90 + \theta) = \sin(90 - (-\theta)) = \cos(-\theta) = \cos \theta
$$

For more formulas, check out the following [cheatsheat](http://tutorial.math.lamar.edu/pdf/trig_cheat_sheet.pdf).

<a name='planar'></a>
## Planar Rotations

Suppose we are given two coordinates frames: a stationary frame of reference $s$ and a body frame $b$. The coordinate frame $b$ is obtained by rotating the frame $s$ counterclockwise by $\theta$ about the origin. We would like to express the unit vectors of the rotated frame $b$ with respect to $s$.

<div class="fig figcenter">
  <img src="/assets/planar-rot.jpg" style="width:500px;"/>
</div>

Because we are dealing with unit-vectors, the math will be equivalent to working in a unit-circle. In fact, $\widehat{x}_b$ will correspond to a rotation by $\theta$ in a unit-circle and $\widehat{y}_b$ to a rotation by $90+ \theta$. This allows us to write the following system of equations, based on the trig properties we derived in the previous section:

$$
\widehat{x}_b = \cos \theta \ \widehat{x}_s + \sin \theta \ \widehat{y}_s \\
\widehat{y}_b = -\sin \theta \ \widehat{x}_s + \cos \theta \ \widehat{y}_s
$$

In matrix form:

$$
  \begin{bmatrix}
    \widehat{x}_b \\
    \widehat{y}_b
  \end{bmatrix}
=
  \begin{bmatrix}
    \cos \theta && \sin \theta \\
    - \sin \theta && \cos \theta \\
  \end{bmatrix}
  \cdot
  \begin{bmatrix}
    \widehat{x}_s \\
    \widehat{y}_s
  \end{bmatrix}
$$

Now consider any point $p$ in both frames. We can look at its coordinates with respect to $s$: $p = p_x \widehat{x}_s + p_y \widehat{y}_s$, or with respect to $b$: $p = p'_x \widehat{x}_b + p'_y \widehat{y}_b$. Again, in matrix form:

$$
\begin{align}
  \begin{bmatrix}
    \widehat{x}_s && \widehat{y}_s
  \end{bmatrix}

  \begin{bmatrix}
    p_x \\
    p_y \\
  \end{bmatrix}
&=
  \begin{bmatrix}
    \widehat{x}_b && \widehat{y}_b
  \end{bmatrix}

  \begin{bmatrix}
    p'_x \\
    p'_y \\
  \end{bmatrix} \\
  &=
    \bigg ( \begin{bmatrix}
    \cos \theta && \sin \theta \\
    - \sin \theta && \cos \theta \\
  \end{bmatrix}
  \cdot
  \begin{bmatrix}
    \widehat{x}_s \\
    \widehat{y}_s
  \end{bmatrix} \bigg )^T
  \begin{bmatrix}
    p'_x \\
    p'_y \\
  \end{bmatrix} \\
  &=
  \begin{bmatrix}
    \widehat{x}_s && \widehat{y}_s
  \end{bmatrix}
\begin{bmatrix}
    \cos \theta && -\sin \theta \\
    \sin \theta && \cos \theta \\
  \end{bmatrix}
  \begin{bmatrix}
    p'_x \\
    p'_y \\
  \end{bmatrix}
\end{align}
$$

Thus, we have shown that

$$
\begin{bmatrix}
    p_x \\
    p_y \\
\end{bmatrix}
=
\overbrace{
\begin{bmatrix}
    \cos \theta && -\sin \theta \\
    \sin \theta && \cos \theta \\
  \end{bmatrix}}^R
\begin{bmatrix}
    p'_x \\
    p'_y \\
\end{bmatrix}
$$

That is, if we have the coordinates of a point in a given frame $b$ and we know the orientation of $b$ with respect to a reference frame $s$, then we can create a rotation matrix $R = [\widehat{x}_b \ \widehat{y}_b]$, where the columns of R are the unit vectors of $b$ as seen in $s$. The matrix R represents a counter-clockwise rotation by an angle $\theta$.

<a name='three-dim'></a>
## Orientation in 3D

Talk about extending to 3D.

We can derive some very nice properties of this rotation matrix. First, since the columns represent the unit vectors in one system as seen by the other, simply taking the transpose will produce the inverse rotation.

<div class="fig figcenter">
  <img src="/assets/flip.jpg" style="width:500px;"/>
</div>

Next, since the columns are unit vectors, then they are pairwise orthogonal. These two properties combine to define the rotation matrix as being orthogonal, $\ie R^T = R^{-1}$.