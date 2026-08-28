---
title: Brief summary of SNN Torch tutorials
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
searchHidden: True # uncomment here -> after completion
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

### [`Tutorial-3`](https://snntorch.readthedocs.io/en/latest/tutorials/tutorial_3.html)



