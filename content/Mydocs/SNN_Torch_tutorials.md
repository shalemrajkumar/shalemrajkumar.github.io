---
title: Brief summary of SNN Torch tutorials
date: 2026-08-23T00:00:00+00:00
summary: "quick review of SNN Torch tutorials"
weight : 0
# aliases: [""]
tags: ["SNN", "SNN_Torch", "tutorial", "NN"]
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
hideSummary: false
searchHidden: false
ShowReadingTime: false
ShowWordCount: false
ShowBreadCrumbs: true
ShowPostNavLinks: true
# cover:
#     image: "<image path/url>" # image path/url
#     alt: "<alt text>" # alt text
#     caption: "<text>" # display caption under cover
#     relative: false # when using page bundles set this to true
#     hidden: true # only hide on current single page
editPost:
     URL: "https://github.com/shalemrajkumar/shalemrajkumar.github.io/content"
     Text: "Suggest Changes" # edit text
     appendFilePath: true # to append file path to Edit link
---

**_This is a brief documentation for my reference from [SNN Torch documentation]()_**

### Reading

-  [The SNNTorch tutorial series is based on the following IEEE paper by JASON et al](https://ieeexplore.ieee.org/abstract/document/10242251)

### [`Tutorial-1: Spike Generation to encode inputs`](https://snntorch.readthedocs.io/en/latest/tutorials/tutorial_1.html) 

#### How to convert datasets into spiking datasets?

Building SNNs we need Input data

So our inputs can be encoded in terms of spikes or could be used directly (in tutorial 3)

<u>basic questions</u>

- Why to encoding data?
- How do brain encodes information? (latency vs firing rate) 
- How long to encode? (number of time steps)
- how many spikes (frequency) to encode?
- how to encode each kind of data? (image, audio, text, etc.)

#### Why to encoding data?

Appeal of encoding data come from the three S's: spikes, sparsity, and static suppression.

- **spikes**
    - Biological neurons process and communicate via spikes (100s of mV in amplitude, 1-2 ms in duration) 
    - Many computational models of neurons simplify this voltage burst to a discrete, single-bit event: a '1' or a '0'. 
    - This is far simpler to represent in hardware than a high precision value.

<span style="display: block; margin-bottom: 1.5rem;">

- **sparsity**
    - Neurons spend most of their time at rest, silencing most activations (in a network) to zero at any given time. 
    - Not only are sparse vectors/tensors (with loads of zeros) cheap to store, but say we need to multiply sparse activations with synaptic weights. If most values are multiplied by '0', then we don't need to read many of the network parameters from memory. This means neuromorphic hardware can be extremely efficient.
    - least overlaping encoding

<span style="display: block; margin-bottom: 1.5rem;">

- **Static-Suppression** (a.k.a, event-driven processing)
    - response to unchanging input is suppressed, so that the network only processes changes in the input. (movement, change in frequency, intensity, etc.)
    - Event-driven processing now only contributes to sparsity and power-efficiency by blocking unchanging input, but it often allows for much faster processing speeds.

<span style="display: block; margin-bottom: 1.5rem;">

<center>
<img src='https://github.com/jeshraghian/snntorch/blob/master/docs/_static/img/examples/tutorial1/3s.png?raw=true' width="600">
</center>

#### Spike Encoding 

MNIST is 28x28 (0-255) grayscale images of handwritten digits.

*How to encode them ?*

Spiking Neural Networks (SNNs) are made to exploit time-varying data, yet MNIST is static. 

There are two options for using MNIST with an SNN:

1. Repeatedly pass the same training sample $\mathbf{X}\in\mathbb{R}^{m\times n}$ to the network at each time step. This is like converting MNIST into a static, unchanging video. Each element of $\mathbf{X}$ can take a high precision value normalized between 0 and 1: $X_{ij}\in [0, 1]$.

<center>
<img src='https://github.com/jeshraghian/snntorch/blob/master/docs/_static/img/examples/tutorial1/1_2_1_static.png?raw=true' width="700">
</center>

2. Convert the input into a spike train of sequence length `num_steps`, where each feature/pixel takes on a discrete value $X_{i,j} \in \{0, 1\}$.
In this case, MNIST is converted into a time-varying sequence of spikes that features a relation to the original image.

<center>
<img src='https://github.com/jeshraghian/snntorch/blob/master/docs/_static/img/examples/tutorial1/1_2_2_spikeinput.png?raw=true' width="700">
</center>


The module `snntorch.spikegen` (i.e., spike generation) contains a series of functions that simplify the conversion of data into spikes. There are currently three options available for spike encoding in `snntorch`:

1. Rate coding: [`spikegen.rate`](https://snntorch.readthedocs.io/en/latest/snntorch.spikegen.html#snntorch.spikegen.rate)
2. Latency coding: [`spikegen.latency`](https://snntorch.readthedocs.io/en/latest/snntorch.spikegen.html#snntorch.spikegen.latency)
3. Delta modulation: [`spikegen.delta`](https://snntorch.readthedocs.io/en/latest/snntorch.spikegen.html#snntorch.spikegen.delta)

How do these differ?


1.   *Rate coding* uses _**input**_ features to determine spiking **frequency**
2.   *Latency coding* uses input _**features**_ to determine spike **timing**
3.   *Delta modulation* uses the _**temporal change**_ of input features to generate spikes


#### Rate coding

One example of converting input data (MNIST) into a rate code is as follows.

- Each normalised input feature $X_{ij}$ is used as the probability an event (spike) occurs at any given time step, returning a rate-coded value $R_{ij}$. 
- This can be treated as a Bernoulli trial: $R_{ij}\sim B(n,p)$, where the number of trials is $n=1$, and the probability of success (spiking) is $p=X_{ij}$. Explicitly, the probability a spike occurs is:

    - $${\rm P}(R_{ij}=1) = X_{ij} = 1 - {\rm P}(R_{ij} = 0)$$
    - example: one input pixel of MNIST with value 0.5 (normalized) will have a 50% chance of spiking at any given time step (here we are using 5 time steps).
    - input_vector = [0.5, 0.5, 0.5, 0.5, 0.5]
    - torch.bernoulli(input_vector) = [0, 1, 0, 1, 1] (randomly generated)



#### How do brain encodes information?

There has been a debate in neuroscience about whether the brain uses rate coding or latency coding.

Work by Bruno A Olshausen title: "What is the other 85 percent of V1 doing" (2004) using the arguments of power consuption and metabolic cost, he argued that the brain mostly uses latency coding by demonstrating that rate-coding can only explain, at most, the activity of 15% of neurons in the primary visual cortex (V1). It is unlikely to be the only mechanism within the brain, which is both resource-constrained and highly efficient.

We know that the reaction time of a human is roughly around 250ms. If the average firing rate of a neuron in the human brain is on the order of 10Hz, then we can only process about 2 spikes within our reaction timescale.

So my belif is that brain uses both rate and latency coding depending on the task and the type of neurons. 

- latency coding: deep cortical neurons (V1, V2, V4) and sensory neurons (auditory, visual, olfactory). 
- rate coding: sensory periphery, motor neurons and some cortical neurons.

But power and latency disadvantages are partually offset by showing huge robustness to noise 

#### latency coding

Temporal codes capture information about the precise firing time of neurons.

a single spike carries much more meaning than in rate codes which rely on firing frequency. 

- susceptibility to noise 
- less power consumed by the hardware running SNN algorithms by orders of magnitude

- For our MNIST example,<span style="display: block; margin-bottom: 1.5rem;">

    - We can use `spikegen.latency` function which allows each input to fire at most once during the full time sweep.
    - Features closer to 1 will fire earlier and features closer to 0 will fire later.
    - Spike timing is calculated by treating the input feature as the current injection $I_{in}$ into an RC circuit.<span style="display: block; margin-bottom: 1.5rem;">

        - This current moves charge onto the capacitor, which increases $V(t)$. We assume that there is a trigger voltage, $V_{thr}$, which once reached, generates a spike. 
        - **The question then becomes**: *for a given input current (and equivalently, input feature), how long does it take for a spike to be generated?*
<break>
        - Starting with Kirchhoff's current law, $I_{in} = I_R + I_C$, the rest of the derivation leads us to a logarithmic relationship between time and the input. 

<center>
<img src='https://github.com/jeshraghian/snntorch/blob/master/docs/_static/img/examples/tutorial1/1_2_4_latencyrc.png?raw=true' width="600">
</center>

##### rate coding vs latency coding visualization

`Rate coding`

![Rate-coded-mnist-5](https://github.com/shalemrajkumar/shalemrajkumar.github.io/blob/main/images/Mydocs/spike_mnist_test.gif?raw=true)

`Latency coding`

![latency-coded-mnist-5](https://github.com/shalemrajkumar/shalemrajkumar.github.io/blob/main/images/Mydocs/mnist_latency.gif?raw=true)

#### Delta Modulation

There are theories that the retina is adaptive: it will only process information when there is something new to process. If there is no change in your field of view, then your photoreceptor cells are  less prone to firing. 

Delta modulation is based on event-driven spiking. The `snntorch.delta` function accepts a time-series tensor as input. It takes the difference between each subsequent feature across all time steps. By default, if the difference is both *positive* and *greater than the threshold $V_{thr}$*, a spike is generated:

<center>
<img src='https://github.com/jeshraghian/snntorch/blob/master/docs/_static/img/examples/tutorial1/1_2_7_delta.png?raw=true' width="600">
</center>

#### code

```python

#%% Imports and Environment Setup %%

import snntorch as snn
import torch

from snntorch import utils
from snntorch import spikegen
from torch.utils.data import DataLoader 

import matplotlib.pyplot as plt
import snntorch.spikeplot as splt
from IPython.display import HTML 

# Training Parameters
batch_size=128
data_path='./data'
num_classes = 10  # MNIST has 10 output classes
num_steps = 100 

# Torch Variables
dtype = torch.float

#%% Download MNIST Dataset %%

from torchvision import datasets, transforms

# Define a transform (not actually doing much, just converting PIL images to tensors)
transform = transforms.Compose([
            transforms.Resize((28,28)),  ## already in same shape
            transforms.Grayscale(),      ## already in grey scale
            transforms.ToTensor(),       ## converts PIL object to tensor
            transforms.Normalize((0,), (1,))]) ## subtracting 0 and dividing by 1
mnist_train = datasets.MNIST(data_path, train=True, download=True, transform=transform)

""" 
Note: This is just an example so we won't be training on whole dataset

- snntorch.utils contains a few useful functions for modifying datasets
- we will use snntorch.utils.data_subset to create a smaller subset of the MNIST dataset.
    - E.g., for subset=10, a training set of 60,000 will be reduced to 6,000.
"""

subset = 10
mnist_train = utils.data_subset(mnist_train, subset)

#%% Creating Dataloaders %%

"""
- The Dataset objects created above load data into memory, and the DataLoader will serve it up in batches. 
- DataLoaders in PyTorch are a handy interface for passing data into a network. 
    - They return an iterator divided up into mini-batches of size batch_size.

why use dataloder instead of for loop ?

* very efficient than "for loop"
* Better through put for the gpu
* shuffle and batching feature for epochs
"""
train_loader = DataLoader(mnist_train, batch_size=batch_size, shuffle=True)

#> read about encoding in the above tutorial 

#%% rate encoding of MNIST dataset %%#

# Iterate through one of minibatches
data = iter(train_loader)
data_it, targets_it = next(data)

# Spiking Data | structure: [num_steps x batch_size x input dimensions]

spike_data_rate = spikegen.rate(data_it, num_steps=num_steps, gain=0.25) # gain reduces the # of spikes so p=1 is not torch.ones(num_steps) i.e always spiking.
spike_data_latency = spikegen.latency(data_it, num_steps=num_steps)
# spike_data_delta = spikegen.delta(data_it, num_steps=num_steps) ## this doesn't work for mnist because it is static representation

# visualize the spike data 

sample_idx = 0
spike_data_rate_sample = spike_data_rate[:, sample_idx, 0]
spike_data_latency_sample = spike_data_latency[:, sample_idx, 0]

print("target:", targets_it[sample_idx].item())

fig, ax = plt.subplots(1, 2, figsize=(8, 4))
ax[0].set_title("Rate Coding")
ax[1].set_title("Latency Coding")

anim_rate = splt.animator(spike_data_rate_sample, fig, ax[0])
anim_latency = splt.animator(spike_data_latency_sample, fig, ax[1])

plt.show()

```

#### Additional docs 

##### **spikegen.rate docs**

- *...*


##### **spikegen.latency docs**

- *...*

##### **spikegen.delta docs**

- *...*

### [`Tutorial-2 LIF Neuron over perceptron`](https://snntorch.readthedocs.io/en/latest/tutorials/tutorial_2.html) 

So if we are using spiking or event driven data, we need a special types of neuron different from traditional perceptron or relu neurons.

so we can go with different levels of abstraction over relu neurons, may be from LIF neuron to Hodgkin-Huxley neuron. But the fact is biology has its limitations and so does our hardware. So we need to find a balance between biological plausibility and hardware efficiency with the primary **goal** in mind.

> We are looking for a event based computation, hoping its is what biology trying to achieve. 

<u>Note:</u> We are missing the spacial computation aspect of it. I am not sure if the delay, refractory period and inhibtion could make up for the missing spacial computation. 

<center>
<img src='https://github.com/jeshraghian/snntorch/blob/master/docs/_static/img/examples/tutorial2/2_1_neuronmodels.png?raw=true' width="1000">
</center>

#### **Leaky Integrate-and-Fire Neuron**

The leaky integrate-and-fire (LIF) neuron, Just like the relu neuron takes a sum of weighted inputs But rather than passing it directly to an activation function, it will integrate the input over time with a leakage, much like an RC circuit. If the integrated value exceeds a threshold, then the LIF neuron will emit a voltage spike. 

The LIF neuron abstracts away the shape and profile of the output spike; it is simply treated as a discrete event. (why? biological importance of spike is to pass the signal along a long axon - but wait there are dendro-axonic, axo-axonic connections in fly) As a result, information is not stored within the spike, but rather the timing (or frequency) of spikes.

Simple spiking neuron models have produced much insight into the neural code, memory, network dynamics, and more recently, deep learning. The LIF neuron sits in the sweet spot between biological plausibility and practicality. 

##### what are we missing in LIF neuron?

- backpropagation of spikes 
- shunting inhibition
- dendritic computation 
- ...

#### Derivation of LIF neuron 

Now say some arbitrary time-varying current $I_{\rm in}(t)$ is injected into the neuron, be it via electrical stimulation or from other neurons. The total current in the circuit is conserved, so:

$$I_{\rm in}(t) = I_{R} + I_{C}$$

From Ohm's Law, the membrane potential measured between the inside and outside of the neuron $U_{\rm mem}$ is proportional to the current through the resistor:

$$I_{R}(t) = \frac{V_{\rm mem}(t)}{R}$$

The capacitance is a proportionality constant between the charge stored on the capacitor $Q$ and $U_{\rm mem}(t)$:


$$Q = CV_{\rm mem}(t)$$

The rate of change of charge gives the capacitive current:

$$\frac{dQ}{dt}=I_C(t) = C\frac{dV_{\rm mem}(t)}{dt}$$

Therefore:

$$I_{\rm in}(t) = \frac{V_{\rm mem}(t)}{R} + C\frac{dV_{\rm mem}(t)}{dt}$$

$$\implies RC \frac{dV_{\rm mem}(t)}{dt} = -V_{\rm mem}(t) + RI_{\rm in}(t)$$

The right hand side of the equation is of units **\[Voltage]**. On the left hand side of the equation, the term $\frac{dV_{\rm mem}(t)}{dt}$ is of units **\[Voltage/Time]**. To equate it to the left hand side (i.e., voltage), $RC$ must be of unit **\[Time]**. We refer to $\tau = RC$ as the time constant of the circuit:

$$ \tau \frac{dV_{\rm mem}(t)}{dt} = -V_{\rm mem}(t) + RI_{\rm in}(t)$$

The passive membrane is therefore described by a linear differential equation.

For a derivative of a function to be of the same form as the original function, i.e., $\frac{dV_{\rm mem}(t)}{dt} \propto V_{\rm mem}(t)$, this implies the solution is exponential with a time constant $\tau$.

Say the neuron starts at some value $U_{0}$ with no further input, i.e., $I_{\rm in}(t)=0$. The solution of the linear differential equation is:

$$V_{\rm mem}(t) = V_0e^{-\frac{t}{\tau}}$$

> In simple terms the injected ions $\rightarrow$ accumulate charge on the membrane + leakage of ions through the leaky channels

Using forward Euler method, we can discretize the differential equation to solve for $V_mem$ at each time step $t$:


$$V(t+\Delta t) = V(t) + \frac{\Delta t}{\tau}\big(-V(t) + RI_{\rm in}(t)\big)$$

simply this can be achieved by [`snntorh.Lapicque`](https://snntorch.readthedocs.io/en/latest/snn.neurons_lapicque.html)

> Add the if condition based threshold and reset mechanism to get the LIF neuron from the Lapicque model (RC circuit).

<u>Note:</u> Most of the tutorial 2 is about coding simple LIF neuron from scratch and comparing with SNN torch Lapicque linking the use of spikegen module as input.

#### code 

```python

# LIF w/Reset mechanism
def leaky_integrate_and_fire(mem, cur=0, threshold=1, time_step=1e-3, R=5.1, C=5e-3):
  tau_mem = R*C
  spk = (mem > threshold)
  mem = mem + (time_step/tau_mem)*(-mem + cur*R) - spk*threshold  # every time spk=1, subtract the threhsold
  return mem, spk

```

### [`Tutorial-3 Simplified LIF neuron and feedforward SNN`](https://snntorch.readthedocs.io/en/latest/tutorials/tutorial_3.html)

We currently had two main concepts covered 

1. How to encode data into spikes 
2. How to build a simple LIF neuron model 

What needs to be covered ?

3. Make a model with encoded inputs, spiking neurons and required architecture to solve a problem (classification, regression, etc.)
4. Training and testing the model 

we will cover these aspects step by step, lets go with building a simple feedforward fully connected SNN model with random inputs generated from `snntorch.spikegen.rate_cov`.

#### Simplified LIF neuron

we will first **simplify** the current LIF model discussed previously to 


<div>
$$U[t+1] = \underbrace{\beta V[t]}_{\mathrm{decay}} + \underbrace{WX[t+1]}_{\mathrm{input}} - \underbrace{S[t]V_{\mathrm{thr}}}_{\mathrm{reset}} \tag{0}$$
</div>


##### <u>**Decay Rate**</u> ($\beta$)

In the previous tutorial, the Euler method was used to derive the following solution to the passive membrane model:

$$V(t+\Delta t) = (1-\frac{\Delta t}{\tau})V(t) + \frac{\Delta t}{\tau} I_{\rm in}(t)R \tag{1}$$

Now assume $I_{\rm in}(t)=0 A$:

$$V(t+\Delta t) = (1-\frac{\Delta t}{\tau})V(t) \tag{2}$$

Let the ratio of subsequent values of $V$, i.e., $V(t+\Delta t)/V(t)$ be the decay rate of the membrane potential, also known as the `inverse time constant`:

$$V(t+\Delta t) = \beta V(t) \tag{3}$$

From $(1)$, this implies that:

$$\beta = (1-\frac{\Delta t}{\tau}) \tag{4}$$

For reasonable accuracy, $\Delta t << \tau$.

If we assume $t$ represents time-steps rather than continuous time (discretize time)

Then we can set $\Delta t = 1$. To further reduce the number of hyperparameters, assume $R=1$. From $(4)$, these assumptions lead to:

$$\beta = (1-\frac{1}{\tau}) \implies (1-\beta)I_{\rm in} = \frac{1}{\tau}I_{\rm in} \tag{5}$$

The input current is weighted by $(1-\beta)$ and also note $\tau$ = C.
By additionally assuming input current instantaneously contributes to the membrane potential:

$$V[t+1] = \beta V[t] + (1-\beta)I_{\rm in}[t+1] \tag{6}$$

<u>Note:</u> The discretization of time means we are assuming that each time bin $t$ is brief enough to fit maximum of one spike in this interval.

##### <u>**Weight**</u> ($W$)

In deep learning, the weighting factor of an input is often a learnable parameter. Taking a step away from the physically viable assumptions made thus far, we subsume the effect of $(1-\beta)$ from $(6)$ into a learnable weight $W$, and replace $I_{\rm in}[t]$ accordingly with an input $X[t]$:

$$WX[t] = I_{\rm in}[t] \tag{7}$$

This can be interpreted in the following way. $X[t]$ is an input voltage, or spike, and is scaled by the synaptic conductance of $W$ to generate a current injection to the neuron. This gives us the following result:

$$U[t+1] = \beta U[t] + WX[t+1] \tag{8}$$


In future simulations, the effects of $W$ and $\beta$ are decoupled.
$W$ is a learnable parameter that is updated independently of $\beta$.


##### <u>**Spiking and Reset**</u>

Recall that if the membrane exceeds the threshold, then the neuron emits an output spike: 


$$S[t] = \begin{cases} 1, &\text{if}~V[t] > V_{\rm thr} \\\
0, &\text{otherwise}\end{cases} \tag{9}$$


If a spike is triggered, the membrane potential should be reset. The *reset-by-subtraction* mechanism is modeled by:

> $$V[t+1] = \beta V[t] + WX[t+1] - S[t]V_{\rm thr} \tag{10}$$

As $W$ is a learnable parameter, and $V_{\rm thr}$ is often just set to $1$ (though can be tuned), this leaves the decay rate $\beta$ as the only hyperparameter left to be specified.

<u>Note:</u> some implementations might make slightly different assumptions. E.g., $S[t] \rightarrow S[t+1]$ in $(9)$, or $X[t] \rightarrow X[t+1]$ in $(10)$. This above derivation is what is used in snnTorch as it maps intuitively to a recurrent neural network representation, without any change in performance.

<br>

```python
def leaky_integrate_and_fire(mem, x, w, beta, threshold=1):
  spk = (mem > threshold) # if membrane exceeds threshold, spk=1, else, 0
  mem = beta * mem + w*x - spk*threshold
  return spk, mem
```

<br>

To set $\beta$, we have the option of either using Eq $(3)$ to define it, or hard-coding it directly. Here, we will use $(3)$ for the sake of a demonstration, but in future, it will just be **hard-coded** as **we are more focused on something that works rather than biological precision**.

Equation $(3)$ tells us that $\beta$ is the ratio of membrane potential across two subsequent time steps. 

Solve this using the continuous time-dependent form of the equation (assuming no current injection), which was derived in [Tutorial 2](#tutorial-2-lif-neuron-over-perceptron):

$$V(t) = V_0e^{-\frac{t}{\tau}}$$

Assume the time-dependent equation is computed at discrete steps of $t, (t+\Delta t), (t+2\Delta t)...$, then we can find the ratio of membrane potential between subsequent steps using:

$$\beta = \frac{V_0e^{-\frac{t+\Delta t}{\tau}}}{V_0e^{-\frac{t}{\tau}}} = \frac{V_0e^{-\frac{t + 2\Delta t}{\tau}}}{V_0e^{-\frac{t+\Delta t}{\tau}}} =...$$
$$\implies \beta = e^{-\frac{\Delta t}{\tau}} $$

#### Feedforward Spiking Neural Network using snnTorch

we are going to use [`snntorch.Leaky`](https://snntorch.readthedocs.io/en/latest/snn.neurons_leaky.html) which is a simplified version of LIF neuron we discussed above. compared to [`snntorch.Lapicque`](https://snntorch.readthedocs.io/en/latest/snn.neurons_lapicque.html) we have to deal with less parameters.

Also `snntorch.Leaky` uses soft reset mechanism which enables better performance in deep learning benchmarks. Not really sure why that is the case. 

<div>
$$V[t+1] = \underbrace{\beta V[t]}_\text{decay} + \underbrace{WX[t+1]}_\text{input} - \underbrace{\beta S[t]V_{\rm thr}}_\text{soft reset} \tag{11}$$
</div>


Now we will create a 3-layer fully-connected neural network of dimensions 784-1000-10 using snnTorch 

<center>
<img src='https://github.com/jeshraghian/snntorch/blob/master/docs/_static/img/examples/tutorial2/2_8_fcn.png?raw=true' width="600">
</center>


> PyTorch routes the neurons together, and snnTorch loads the results into spiking neuron models. In terms of coding up a network, these spiking neurons can be treated like time-varying activation functions.



```python

# imports
import snntorch as snn
from snntorch import spikeplot as splt
from snntorch import spikegen

import torch
import torch.nn as nn
import matplotlib.pyplot as plt 


num_steps = 200

# layer parameters
num_inputs = 784
num_hidden = 1000
num_outputs = 10

beta = 0.99

# initialize layers
fc1 = nn.Linear(num_inputs, num_hidden)
lif1 = snn.Leaky(beta=beta)
fc2 = nn.Linear(num_hidden, num_outputs)
lif2 = snn.Leaky(beta=beta)

# Initialize hidden variables and outputs of each neuron

"""
As networks increase in depth, this becomes more tedious to initial state variables like mem. The static method init_leaky() can be used to take care of this by creating the correctly-shaped, zeroed-out initial membrane potential tensor for that layer, also each neuron type have their own init methods
"""

mem1 = lif1.init_leaky()
mem2 = lif2.init_leaky()

# record outputs
mem2_rec = []
spk1_rec = []
spk2_rec = []

"""
- We create a random input spike train to pass to the network with 200 timesteps and 784 neurons
- Usually neural nets process data in batches and snnTorch uses dim "1" as the batch dimension
"""

spk_in = spikegen.rate_conv(torch.rand((200, 784))).unsqueeze(1)

"""
- In terms of coding up a network, these spiking neurons can be treated like time-varying activation functions.

- Here is a sequential account of what's going on:

* The $i^{th}$ input from `spk_in` to the $j^{th}$ neuron is weighted by the parameters initialized in `nn.Linear`: $X_{i} \times W_{ij}$ (similar to W.T@X)
* This generates the input current term from Equation $(10)$, contributing to $V[t+1]$ of the spiking neuron (voltage rises from rest)
* If $V[t+1] > V_{\rm thr}$, then a spike is triggered from this neuron (threshold check -> spike generation)
* This spike is weighted by the second layer weight, and the above process is repeated for all inputs, weights, and neurons.

Note: Now we are now scaling the input current with a weight generated by `nn.Linear`, rather than manually setting W ourselves.
"""

# network simulation
for step in range(num_steps):
    cur1 = fc1(spk_in[step]) # post-synaptic current <-- spk_in x weight
    spk1, mem1 = lif1(cur1, mem1) # mem[t+1] <--post-syn current + decayed membrane
    cur2 = fc2(spk1)
    spk2, mem2 = lif2(cur2, mem2)

    mem2_rec.append(mem2)
    spk1_rec.append(spk1)
    spk2_rec.append(spk2)

# convert lists to tensors
mem2_rec = torch.stack(mem2_rec)
spk1_rec = torch.stack(spk1_rec)
spk2_rec = torch.stack(spk2_rec)


fig, ax = plt.subplots(3, figsize=(8,7), sharex=True, 
                        gridspec_kw = {'height_ratios': [1, 1, 0.4]})

# Plot input spikes
splt.raster(spk_in[:,0], ax[0], s=0.03, c="black")
ax[0].set_ylabel("Input Spikes")
ax[0].set_title("Fully Connected Spiking Neural Network")

# Plot hidden layer spikes
splt.raster(spk1_rec.reshape(num_steps, -1), ax[1], s = 0.05, c="black")
ax[1].set_ylabel("Hidden Layer")

# Plot output spikes
splt.raster(spk2_rec.reshape(num_steps, -1), ax[2], c="black", marker="|")
ax[2].set_ylabel("Output Spikes")
ax[2].set_ylim([0, 10])

plt.show()


```

![random input-output snn](https://github.com/shalemrajkumar/shalemrajkumar.github.io/blob/main/images/Mydocs/random_io_snn.png?raw=true)

At this stage this is just a random input spike trains, random weights and random outputs. We need to train the network to get meaningful inputs, outputs.

### [`Tutorial-4 `](https://snntorch.readthedocs.io/en/latest/tutorials/tutorial_4.html)

Till now we have seen whenever there is input current there is instantaneous response in the $V_m$ which is fixed by soft reset but still we have instantaneous synaptic current when presynaptic neuron spikes but in reality post neuronal input current (prev_neuron spike $\rightarrow$ travel via axon $\rightarrow$ synaptic neurotransmitter release $\rightarrow$ post_neuron) gradually grows and decays with some delay.

Currently I am not really sure on functional aspects of **delayed post synaptic current**, **non linearity** associated with this post synaptic current (bi-exponential growth and decay).

Now only transmitter release dynamics but also neurotransmitters activate the post-synaptic receptors, which directly influence the effective current that flows into the post-synaptic neuron. Shown below are two types of excitatory receptors, AMPA and NMDA.

 
<center>
<img src='https://github.com/jeshraghian/snntorch/blob/master/docs/_static/img/examples/tutorial2/2_6_synaptic.png?raw=true' width="600">
</center>

The simplest model of synaptic current assumes an increasing current on a very fast time-scale, followed by a relatively slow exponential decay, as seen in the AMPA receptor response above. This is very similar to the membrane potential dynamics of Lapicque's model.


The synaptic model has two exponentially decaying terms: $I_{\rm syn}(t)$ and $U_{\rm mem}(t)$. The ratio between subsequent terms (i.e., decay rate) of $I_{\rm syn}(t)$ is set to $\alpha$, and that of $U(t)$ is set to $\beta$:

$$ \alpha = e^{-\Delta t/\tau_{\rm syn}}$$

$$ \beta = e^{-\Delta t/\tau_{\rm mem}}$$

where the duration of a single time step is normalized to $\Delta t = 1$ in future. $\tau_{\rm syn}$ models the time constant of the synaptic current in an analogous way to how $\tau_{\rm mem}$ models the time constant of the membrane potential. $\beta$ is derived in the exact same way as the previous tutorial, with a similar approach to $\alpha$:

$$I_{\rm syn}[t+1]=\underbrace{\alpha I_{\rm syn}[t]}_\text{decay} + \underbrace{WX[t+1]}_\text{input}$$

$$U[t+1] = \underbrace{\beta V[t]}_\text{decay} + \underbrace{I_{\rm syn}[t+1]}_\text{input} - \underbrace{R[t]}_\text{reset}$$

The same conditions for spiking as the previous LIF neurons still hold:

$$S_{\rm out}[t] = \begin{cases} 1, &\text{if}~V[t] > V_{\rm thr} \\\
0, &\text{otherwise}\end{cases}$$

#### Synaptic Neuron Model

we can use [`snnTorch.Synaptic`](https://snntorch.readthedocs.io/en/latest/snn.neurons_synaptic.html) to achive this 2nd-Order Integrate-and-Fire Neuron (including synaptic conductance)

* $\alpha$: the decay rate of the synaptic current
* $\beta$: the decay rate of the membrane potential (as with Lapicque)

<center>
<img src='https://github.com/jeshraghian/snntorch/blob/master/docs/_static/img/examples/tutorial2/2_7_stein.png?raw=true' width="600">
</center>

Each spike contributes a shifted exponential decay to the synaptic current $I_{\rm syn}$, which are all summed together. This current is then integrated by the passive membrane equation derived earlier in tutorial 2

<u>**When to use 1st or 2nd order neurons ?**</u>

<u style="text-decoration: underline dashed; text-underline-offset: 4px;">**When 2nd-order neurons are better**</u>
* If the temporal relations of your input data occur across long time-scales,
* or if the input spiking pattern is sparse

By having two recurrent equations with two decay terms ($\alpha$ and $\beta$), this neuron model is able to 'sustain' input spikes over a longer duration. This can be beneficial to retaining long-term relationships.

An alternative use case might also be:

* When temporal codes matter

> If you care for the precise timing of a spike, it seems easier to control that for a 2nd-order neuron. In the `Leaky` model, a spike would be triggered in direct synchrony with the input. For 2nd-order models, the membrane potential is 'smoothed out' (i.e., the synaptic current model low-pass filters the membrane potential), which means $V[t]$ experiences a finite rise time. This is clear from the above image, where the output spikes experience a delay with respect to the input spikes.

<u style="text-decoration: underline dashed; text-underline-offset: 4px;">**When 1st-order neurons are better**</u>
* Any case that doesn't fall into the above, and sometimes, the above cases.

By having one less equation in 1st-order neuron models (such as `Leaky`), the backpropagation process is made a little simpler. Though having said that, the `Synaptic` model is functionally equivalent to the `Leaky` model for $\alpha=0$. 

In Jason's own hyperparameter sweeps on simple datasets, the optimal results seem to push $\alpha$ as close to 0 as possible. As data increases in complexity, $\alpha$ may grow larger.


#### Alpha Neuron model 

Alpha neuron model is a class of Spike Response Model (SRM), we need to understand SRM class of neuron models.

SRM is a generalization of LIF that describes a neuron's membrane potential *not* through a differential equation, but through kernels (response functions) convolved with input spikes. 

SRM directly writes the membrane potential as a sum of postsynaptic potentials (PSPs) triggered by each incoming spike, plus a reset/refractory kernel triggered by the neuron's own past spikes:

<div>
$$ V(t)=\underbrace{\sum _{f}\eta (t-t^{f})}_\text{effect of own past spikes: reset}+\underbrace{\int _{0}^{\infty }\kappa (s)I(t-s)\,ds}_\text{effect of incoming spikes}+V_{rest} $$
</div>


$\kappa$ : the kernel describing how much a single input spike raises the membrane potential over time (the shape of one PSP).

$\eta$ : the refractory kernel describing how the neuron's own spike suppresses further firing right after.

So SRM is essentially: "skip solving the ODE — just define the shape of the response to a spike directly, and stack them up." It's more general than LIF because you can pick any kernel shape you like.

> SRM models are appealing as they can arbitrarily add refractoriness, threshold adaptation, and any number of other features simply by embedding them into the filter. 

<center>
<img src='https://github.com/jeshraghian/snntorch/blob/master/docs/_static/img/examples/tutorial2/exp.gif?raw=true' width="400">
</center> 

<figure style="text-align: center;">
  <img src="https://github.com/jeshraghian/snntorch/blob/master/docs/_static/img/examples/tutorial2/alpha.gif?raw=true" width="400" alt="Spike response to different kernels">
  <figcaption>spike to response from different kernels</figcaption>
</figure>

<br>

The **Alpha neuron model** is SRM with a particular choice of kernel: the **alpha function** (rises and decays)

$$V_{\rm mem}(t) = \sum_i W(\kappa * S_{\rm in})(t)$$

where the incoming spikes $S_{\rm in}$ are convolved with a spike response kernel $\kappa( \cdot )$. The spike response is scaled by a synaptic weight, $W$. In the figures above, the top kernel is an exponentially decaying function and would be the equivalent of Lapicque's 1st-order neuron model. On the bottow, the kernel is an alpha function:

$$\kappa(t) = \frac{t}{\tau}e^{1-t/\tau}\Theta(t)$$

where $\tau$ is the time constant of the alpha kernel and $\Theta$ is the Heaviside step function. Most kernel-based methods adopt the alpha function as it provides a time-delay that is useful for temporal codes that are concerned with specifying the exact spike time of a neuron. 

> In snnTorch, the spike response model is not directly implemented as a filter. Instead, it is recast into a recursive form such that only the previous time step of values are required to calculate the next set of values. This significantly reduces the memory overhead during learning.


<center>
<img src='https://github.com/jeshraghian/snntorch/blob/master/docs/_static/img/examples/tutorial2/2_9_alpha.png?raw=true' width="600">
</center> 


As the membrane potential is now determined by the sum of two exponentials, each of these exponents has their own independent decay rate. $\alpha$ defines the decay rate of the positive exponential, and $\beta$ defines the decay rate of the negative exponential.

Usage of [`snnTorch.Alpha`](https://snntorch.readthedocs.io/en/latest/snn.neurons_alpha.html) is similar to previous neurons except we need divide synaptics currents into positive and negative.

 Alpha neuron models are included with the intent of providing an option for porting across SRM-based models over into snnTorch, although natively training them seems to not be too effective, because we need to separate positive and negative currents.  


>  In general, **Leaky** and **Synaptic** seem to be the most useful for training a network.
