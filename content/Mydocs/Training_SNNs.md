---
title: Training SNNs
weight : 0
tags: ["SNN", "Torch", "Neural Networks", "Training"]
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
ShowBreadCrumbs: true
ShowPostNavLinks: true
---

> [!WARNING]
> this is a work in progress.

### SNNs and Surrogate gradient descent

Spiking neural networks (SNNs) are biologically inspired models that compute via discrete, sparse spikes, rather than continuous activations (non differentiable non linearity). This event driven framework not only captures **rich temporal patterns** (such as inter spike intervals and cross neuron synchrony) but also powers energy efficient neuromorphic hardware.

Surrogate gradient descent (SuGD) answer the most important challenging question: "how to convert precise spike timing into effective learning signals ?"

This problem is solved by Zenke *et al* 2018 through replacing the non differentiable spike with a smooth surrogate, thereby allowing backpropagation through time (BPTT).

 SuGD can learn to make use of information that is not only encoded in the rate of spikes but also timing. SGD training can extract interspike intervals, spatio-temporal spike patterns or polychrony, and coincidence codes ([Ziqiao Yu *et al* 2026](10.1088/2634-4386/ae46d5))[1].

#### Example

<u>*Heavy side function*</u>

$$H(x) = \begin{cases} 0 & x < 0 \\\ 1 & x \geq 0 \end{cases}$$

<u>*D(Heavy side function)*</u>

$$\frac{dH(x)}{dx} = \begin{cases} 0 & x < 0 \\\ \text{undefined} & x \geq 0 \end{cases}$$

#### Surrogate approximation

using a sigmoid or fast sigmoid function (kind of cheat:)

<u>*sigmoid or logistic function*</u>

$$\sigma(x) = \frac{1}{1 + e^{-\beta x}}$$

<u>*D(sigmoid or logistic function)*</u>

$$\frac{d\sigma(x)}{dx} = \beta\sigma(x)(1 - \sigma(x))$$


<u>*fast sigmoid function*</u>

$$\sigma(x) = \frac{x}{1 + |x|}$$

<u>*D(fast sigmoid function)*</u>

$$\frac{d\sigma(x)}{dx} = \frac{1}{(1 + |x|)^2}$$

`Code`

$$\text{heaviside}(x) \approx \sigma(x)$$

```python
class SurrogateHeaviside(torch.autograd.Function):
    @staticmethod
    def forward(ctx, input):
        ctx.save_for_backward(input)
        return torch.heaviside(input, 0)
    @staticmethod
    def backward(ctx, grad_output):
        input, = ctx.saved_tensors
        beta = 5
        s = torch.sigmoid(beta*input)
        grad = grad_output*beta*s*(1-s)
        return grad
surrogate_heaviside = SurrogateHeaviside.apply
```

$$\text{heaviside}(x) \approx fast\_sigmoid(x)$$

```python
class SurrogateHeaviside(torch.autograd.Function):
    scale = 100.0 # controls steepness of surrogate gradient
    @staticmethod
    def forward(ctx, input):
        ctx.save_for_backward(input) ## storing context or input to use during backprop
        out = torch.zeros_like(input) ## first step make everything zero (i.e. all negative inputs are zero)
        out[input > 0] = 1.0 ## mask the inputs > 0 and make them 1
        return out
    @staticmethod
    def backward(ctx, grad_output):
        input, = ctx.saved_tensors ## fetching previously saved input
        grad_input = grad_output.clone() ## safely copying the gradient output so we don't accidentally break PyTorch graph
        grad = grad_input/(SurrogateHeaviside.scale*torch.abs(input)+1.0)**2 ## computing fast sigmoid instead of using the e^x which takes longer to run
        return grad
surrogate_heaviside  = SurrogateHeaviside.apply
```
*credits: Tutoral by Dan F M Goodman, 2026 FENS Chen Summer School on Learning with spikes [2]*


### <u>Tutorial: SuGD for training SNNs</u>

<br>

> _This section is inspired snnTorch [tutorial-5](https://snntorch.readthedocs.io/en/latest/tutorials/tutorial-5.html) which is in turn inspired by  Friedemann Zenke's extensive work on SNNs. Check out his repo on surrogate gradients [spytorch](https://github.com/fzenke/spytorch)_

<br>

Now we will implement a basic supervised learing algorithm with SuGD for training spiking neurons to perform image classfication on Static MNIST. 

#### The Recurrent representation of SNNs

If you think of SNNs like a Machine learning researcher you can tell it is type of RNNs with implict recurrence with nonlinear activation function.

Further Infromation on RNNs are found [here]()

<u>LIF Neuron discrete recursive form</u>

<div>
$$U[t+1] = \underbrace{\beta U[t]}_\text{decay} + \underbrace{WX[t+1]}_\text{input} - \underbrace{R[t]}_\text{reset}$$
</div>

where if the membrane potential exceeds the threshold, a spike is emitted:

$$S[t] = \begin{cases} 1, &\text{if}~U[t] > U_{\rm thr} \\\
0, &\text{otherwise}\end{cases}$$


<figure style="text-align: center;">
  <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/b/b5/Recurrent_neural_network_unfold.svg/500px-Recurrent_neural_network_unfold.svg.png?utm_source=en.wikipedia.org&utm_campaign=parser&utm_content=thumbnail"width="400" alt="Vanilla-RNN">
  <figcaption>image credit: wikipedia</figcaption>
</figure>

Like in RNNs, we have dependence from previous state (here state variable $U[t]$), Also its not even a property of network just the neuron itself.This is illustrated using an *implicit* recurrent connection for the decay of the membrane potential.

<u> Vanilla-RNN </u>

hidden state: $$ h_t = f\left(W_{ih} * x_t + W_{hh} * h_{t-1} + b_h)\right) $$

output: $$ y_t = W_{ho} * h_t + b_o $$

This is almost perfectly poised to take advantage of the developments in training recurrent neural networks (RNNs) and sequence-based models.

there is the special aspect of recurrence called **_explict recurrence_** in SNNs  where the output spike $S_{\rm out}$ is fed back to the input, i.e. neurons in same layer are connected to each other or even to itself simlar to [`RLeaky`]() in snnTorch and these connection ($W_{recurr}$ where diagonals represent self connections and off diagonal elements represent recurrent connection between different neurons) fully trainable.

 In the figure below, the connection weighted by $-U_{\rm thr}$ represents the reset mechanism $R[t]$.

<center>
<img src='https://github.com/jeshraghian/snntorch/blob/master/docs/_static/img/examples/tutorial5/unrolled_2.png?raw=true' width="800">
</center>

The benefit of an unrolled graph is that it provides an explicit description of how computations are performed. The process of unfolding illustrates the flow of information forward in time (from left to right) to compute outputs and losses, and backward in time to compute gradients. The more time steps that are simulated, the deeper the graph becomes. 

Conventional RNNs treat $\beta$ as a learnable parameter. This is also possible for SNNs, though by default, they are treated as hyperparameters. This replaces the vanishing and exploding gradient problems with a hyperparameter search. 


 







### Additional details on SuGD 

##### Foundational details

- Neftci, Mostafa & Zenke (2019)
- Eshraghian, Ward, Neftci et al. (2021/2023)

##### reset mechanisms

- Zenke & Vogels (2021)

##### synaptic/membrane dynamics

- Wu et al. (2018),

##### Initializations

- Rossbroich, Gygax & Zenke (2022), "Fluctuation-Driven Initialization for Spiking Neural Network Training," Neuromorphic Computing and Engineering
- Micheli et al. (2024/2025), "Deep Activity Propagation via Weight Initialization in Spiking Neural Networks"

##### miscellaneous

- Direct Training High-Performance Deep Spiking Neural Networks: A Review of Theories and Methods
- Fractional-order Spiking Neural Network

### Pytorch tricks

### Pytorch drawbacks

### interesting work on SNNs and training SNNs

### what are we missing in the SNNs ?

### References

1. [Ziqiao Yu et al 2026 Neuromorph. Comput. Eng. 6 014016](10.1088/2634-4386/ae46d5)
2. [Dan F M Goodman, 2026 FENS Chen Summer School on Learning with spikes](https://github.com/neural-reckoning/cambridge-fens-chen-summer-school-2026)


