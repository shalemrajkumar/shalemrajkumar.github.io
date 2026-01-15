---
title: My RNN Documentation
weight : 0
tags: ["RNN", "Neural Networks", "Deep Learning"]
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
searchHidden: true
ShowReadingTime: false
ShowBreadCrumbs: true
ShowPostNavLinks: true
---


## Introduction

RNNs are first working initiative to process sequential data by maintaining a hidden state that captures information about previous elements (some early varient of context) in the sequence. They are widely used in various applications such as language modeling, speech recognition, and time series prediction.

Different types of sequential data include:

- Text data (e.g., sentences, documents)
- Time series data (e.g., stock prices, weather data)
- Audio data (e.g., speech signals, music)
- Video data (e.g., frames in a video sequence)
- Biological sequences (e.g., DNA, protein sequences)
- sequence of actions (e.g., user behavior, robot movements)

Different ways to implement RNNs include:

- point neurons based RNNs (optimized with backpropagation through time)
- spiking neurons based RNNs (optimized with surrogate gradient descent)

Different architectures of RNNs include:
- [Vanilla RNNs](#vanilla-rnn)
- [Long Short-Term Memory (LSTM) networks](#long-short-term-memory)
- Gated Recurrent Units (GRU)
- Bidirectional RNNs
- Deep RNNs

RNNs in biological neural systems are core of every cognitive function, they are responsible for information processing, maintaining working memory, and generating temporal patterns of activity that facilitate learning. Feedback is basic subunit of intelligence in biological systems!

Neuroscience models:
- Elman Networks
- Jordan networks
- Hopfield Networks
- Echo State networks
- Liquid State Machines


Neuroscience applications of RNNs:
- Working Memory
- Sequence Generation
- Temporal Pattern recognition
- Motor Control
- Spontaneous Activity based circuit refinement


## Point neuron based RNNs

### Vanilla RNN

#### algorithm

1. Initialize weights and biases
2. Compute forward pass
    - at t=0
        - Initialize hidden state: $$ h_0  = 0 $$
    - For each time step t:
        - Compute hidden state: $$ h_t = f\left(W_{ih} * x_t + W_hh * h_{t-1} + b_h)\right) $$
        - Compute output: $$ y_t = W_ho * h_t + b_o $$
        - [Optional] Apply activation function to output: $$ y_t = g(y_t) $$
3. Compute loss
4. Backpropagate errors through time
5. Update weights and biases
6. Repeat for multiple epochs

#### limitations and motivations for advanced architectures 

Classic RNNs can keep track of arbitrary long-term dependencies in the input sequences. But during training via back-propagation, the long-term gradients which are back-propagated can "vanish", RNNs using LSTM units partially solve the vanishing gradient problem, because LSTM units allow gradients to also flow with little to no attenuation. However, LSTM networks can still suffer from the "exploding gradient problem".


### Long Short-Term Memory

#### introduction

LSTMs architecture is designed to address the vanishing gradient problem in traditional RNNs by introducing *memory cells* and *gating mechanisms* that allow the network to maintain and update information over longer sequences (usually short-term memory for RNN that can last thousands of timesteps (thus longterm))

An LSTM unit is typically composed of a cell and three gates:

- **Cell State**: 
    -Carries information across arbitary time steps. while the gates regulate the flow of information into and out of the cell state.
- **Forget Gate**: 
    - Determines which information from the previous cell state should be discarded. 
    - It maps the previous state and the current input to a value between 0 and 1 (after rounding 0 $\rightarrow$ completely forget, 1 $\rightarrow$ completely retain)
- **Input Gate**: 
    - Input gates decide which pieces of new information to store in the current cell state. ( similar mechanism like forget gates) 
- **Output Gate**: 
    - Output gates control which pieces of information in the current cell state to output. (similar mechanisms)

#### algorithm

1. Initialize weights and biases

#### additional resources and documentation

- [mydocs]() 

## Conclusion

Recurrent Neural Networks are inspired from the neuroscience, a fully cross-coupled perceptron network is equivalent to an infinitely deep feedforward network but we train these networks with backpropagation which has limitations such as vanishing and exploding gradients. To overcome these limitations, more advanced architectures like LSTM and GRU were developed, but the fundamental question still remains, "how our brain recurrent motifs learn ?"

## References


## Additional references and tutorials

### Vanilla RNN tutorials

- [wiki](https://en.wikipedia.org/wiki/Recurrent_neural_network)
- [RNNs by torch](https://docs.pytorch.org/docs/stable/generated/torch.nn.RNN.html)
- [RNN tutorial from kaggle](https://www.kaggle.com/code/namanmanchanda/rnn-in-pytorch)
- [RNN tutorial from medium](https://medium.com/@noorfatimaafzalbutt/recurrent-neural-networks-rnn-with-pytorch-a-complete-guide-8c40c69032d2)
- [RNN tutorial from data_academy](https://www.codecademy.com/article/rnn-py-torch-time-series-tutorial-complete-guide-to-implementation)
- [RNN tutorial by solardevs](https://solardevs.com/blog/rnn-from-scratch-pytorch/)
- [RNN tutorial by deeplearning wizard](https://www.deeplearningwizard.com/deep_learning/practical_pytorch/pytorch_recurrent_neuralnetwork/#steps_2)
- [RNN tutorial by analyticsvidhya](https://www.analyticsvidhya.com/blog/2021/07/understanding-rnn-step-by-step-with-pytorch/)
- [RNN tutorial APX](https://apxml.com/courses/getting-started-with-pytorch/chapter-7-introduction-common-architectures/building-simple-rnn)
- [RNN tutorial by Jake](https://jaketae.github.io/study/pytorch-rnn/)
- [RNN tutorial by Utoronto](https://www.cs.toronto.edu/~lczhang/aps360_20191/lec/w06/rnn.html)
- [RNN tutorial by towardsdatascience](https://towardsdatascience.com/rnns-from-theory-to-pytorch-f0af30b610e1/)

