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

##### Example

`Heavy side function`

$$H(x) = \begin{cases} 0 & x < 0 \\ 1 & x \geq 0 \end{cases}$$

*D(Heavy side function)*

$$\frac{dH(x)}{dx} = \begin{cases} 0 & x < 0 \\ \text{undefined} & x = 0 \\ 0 & x > 0 \end{cases}$$

**_Surrogate approximation_**

using a sigmoid or fast sigmoid function (kind of cheat:)

*sigmoid or logistic function*

$$\sigma(x) = \frac{1}{1 + e^{-\beta x}}$$

*D(sigmoid or logistic function)*

$$\frac{d\sigma(x)}{dx} = \beta\sigma(x)(1 - \sigma(x))$$


*fast sigmoid function*

$$\sigma(x) = \frac{x}{1 + |x|}$$

*D(fast sigmoid function)*

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

### forward pass

### backward pass

### how to deal with RNNs

### Can i have synaptic nonlinearities ?

### can i have trainable parameters for the synapses ?

### Pytorch tricks

### Pytorch drawbacks

### My SNN torch docs

### example tutorial

### interesting work on SNNs and training SNNs

### what are we missing in the SNNs ?

### References

1. [Ziqiao Yu et al 2026 Neuromorph. Comput. Eng. 6 014016](10.1088/2634-4386/ae46d5)
2. [Dan F M Goodman, 2026 FENS Chen Summer School on Learning with spikes](https://github.com/neural-reckoning/cambridge-fens-chen-summer-school-2026)


