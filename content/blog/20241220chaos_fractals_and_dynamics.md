

---
title: Chaos, fractals and dynamics
summary: "Chapter 1 : Nonlinear dynamics and chaos by Steven Strogatz"
weight : 1
# aliases: [""]
tags: ["chaos", "dynamical systems", "fractals", "nonlinear dynamics", "strogatz"]
author: "sraj"
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: true
# description: ""
# canonicalURL: "https://canonical.url/to/page"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
# cover:
#     image: "<image path/url>" # image path/url
#     alt: "<alt text>" # alt text
#     caption: "<text>" # display caption under cover
#     relative: false # when using page bundles set this to true
#     hidden: true # only hide on current single page
# editPost:
#     URL: "https://github.com/kiwamizamurai/content"
#     Text: "Suggest Changes" # edit text
#     appendFilePath: true # to append file path to Edit link
---


#### Introduction

  -  Dynamical systems are core of fractals and chaos and we have some great emergent properties as the dimensionality increase however we can only understand deviation as the dimensionality increases through Lyapunav exponent. 
  - Lets see how far our understanding has reached in interpreting differential equations with some awesome example curated by steven stogzartz book "Non linear dynamics and chaos"
  - We look at dynamical systems in terms geometric view 
#### History of Dynamics
> Newton laws of motion
- It was branch of physics where it all started in mid 1600 when Newton invented differential equations, discovered laws of motion and universal gravitation, and combined with Kepler's laws of planetary motion
- Newton solved the two body problem (sun and earth system)
> Three body problem
- But when people tried to extend it to three body problem it was realized its impossible to solve.
> Poincare geometrical view
- Now Poincare in late _1800_ asked the different question "**will our solar system be stable forever**", he laid the foundations of modern dynamics with his innovative geometric interpretation.
- he is the first person to have a partial view on the possibility of chaos
> Non linear oscillators
- people in the beginning of _1900_ are interested in non linear oscillators
	- Applications in engineering and physics
		- radio
		- radar
		- phase-locked loops
		- lasers
- Poincare geometrical methods are further developed to better understanding of classical mechanics
> Era of computation, lorrentz and fractals
- In 1950 the we got access to high speed computing where we used Euler way of to simulate dynamical systems such a way that no one can ever imagine before
$$ x_{t+1} = x_{t} + dx $$
- This helped people to develop more intuition on non linear dynamics
- Lorentz simulated a strange attractor (when he is studying convection rolls in the atmosphere) that oscillated in a irregular and aperiodic fashion which is highly sensitive to initial conditions.  This was the initial observations of chaos
- The Lorenz system is defined by the following equations:

$$
\frac{dx}{dt} = \sigma (y - x)
$$

$$
\frac{dy}{dt} = x (\rho - z) - y
$$

$$
\frac{dz}{dt} = x y - \beta z
$$

Where:
- \(x\), \(y\), and \(z\) are the variables representing the system's state in 3D space.
- $\sigma$, $\rho$, and $\beta$ are parameters controlling the system's behavior.
- All the solution fell onto a butterfly shaped set of points. "an infinite complex of surfaces" - fractal.

![Animated Lorenz system attractor](https://miro.medium.com/v2/resize:fit:640/format:webp/1*6ehwW04jwunImzrhYKRlbQ.gif)
[code](https://github.com/gboeing/lorenz-system)
credits : Shreemoykee

> Era of chaos: late 1970s
- Ruelle and Takens proposed a new theory for the onset of turbulence in fluids
- May found examples of chaos in iterated mappings of population biology
- Feigenbaum discovered universal laws governing the transition from regular to chaotic behavior. (link between chaos and phase transition)
- Mandelbrot popularized fractals
- Winfree applied the applied geometric methods of dynamics to biological oscillations.

> Section summary
 ##### Dynamics — A Capsule History

| Year/Period   | Contributor(s)                                        | Key Developments                                                                                                                                                          |
| ------------- | ----------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1666          | Newton                                                | Invention of calculus, explanation of planetary motion                                                                                                                    |
| 1700s–1800s   |                                                       | Flowering of calculus and classical mechanics, analytical studies of planetary motion                                                                                     |
| 1890s         | Poincaré                                              | Geometric approach, nightmares of chaos                                                                                                                                   |
| 1920–1950     |                                                       | Nonlinear oscillators in physics and engineering; invention of radio, radar, laser                                                                                        |
| 1920–1960     | Birkhoff, Kolmogorov, Arnold, Moser                   | Complex behavior in Hamiltonian mechanics                                                                                                                                 |
| 1963          | Lorenz                                                | Strange attractor in simple model of convection                                                                                                                           |
| 1970s         | Ruelle & Takens, May, Feigenbaum, Winfree, Mandelbrot | Turbulence and chaos; chaos in logistic map; universality and renormalization, connection between chaos and phase transitions; nonlinear oscillators in biology; fractals |
| 1980s         |                                                       | Widespread interest in chaos, fractals, oscillators, and their applications                                                                                               |
| 1990s–present |                                                       | Complex systems, networks, collective behavior                                                

#### Importance of being nonlinear

> Dynamical systems are often denoted with `Differential equations` {continuous time} and `iterated maps (difference equations)` {discrete time}
- Now we focus on differential equations
	- we have Ordinary differential equations (ODEs) and Partial differential equations (PDEs).

#### Dynamical view of the world

- We have linear ODEs and PDEs which are very common in physics and engineering
- But we also have nonlinear ODEs and PDEs which are complex, most common in nature and least understood.
- This book by Strogatz approaches these nonlinear ODEs and PDEs with a geometric view to understand the dynamics of the system even if we cannot solve analytically.

<!--
  Title: Chaos, fractals and dynamics
  Description: chapter 1 of nonlinear dynamics and chaos
  Author: raj
  start date: 20-12-2024
  -->

