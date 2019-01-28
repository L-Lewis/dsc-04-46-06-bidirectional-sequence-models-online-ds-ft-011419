
# Bidirectional Sequence Models

## Introduction

In this lesson, we'll learn about how **_Bidirectional Layers_** work, and pros and cons of including them in our Sequence Models!

## Objectives

You will be able to:

* Understand and explain the basic architecture of a Bidirectional RNN
* Identify the types of problems Bidirectional approaches are best suited for
* Build and train Bidirectional RNN models

## The Problem of Sequences 

Consider the following sentences:

> He said, "Teddy bears are on sale!"
> He said, "Teddy Roosevelt is my favorite president!"

Let's assume we we've built a Sequence Model for Named Entity Recognition, where the model's job is to output a 1 for any words that are part of a named entity, and a 0 for any words that are not. In the first example, there is no named entity, and the model should output a 0 for every word. In the second example, "Teddy Roosevelt" is a named entity, so the model should output a 1 for "Teddy", a 1 for "Roosevelt", and a 0 for every other word in the sentence.

Now, since this is being fed into an RNN, this means that the model is making its predictions one word at a time, and then passing this info along as input for each subsequent time step (remember, when working with text data in Sequence Models, each word is a time step). In either of the sentences above, how does the model know what to do when it gets to the third time step and encounters the word "Teddy"? As we can see from the examples above, it could go either way. It really depends on if the word "Teddy" is followed by the word "bear" or the word "Roosevelt". However, the model can't see into the future--it has to work through the data sequentially (hence the name, _Sequence_ Model). This means that the model doesn't know what the fourth word in the sentence when making a prediction on step 3--just the first and second words. This means that in it's current state, this is an example of a problem that a basic RNN/LSTM/GRU model will perform poorly at, because it can't see the future. 

This is where Bidirectional RNNs come in!

## Bidirectional RNNs

Luckily, there is an architecture that can solve this sort of problem quite easily--a **_Bidirectional RNN_**! A Bidirectional RNN is just like a regular RNN, but with a twist--half of the neurons start by at the beginnig of the data and work towards the end one step at a time, while the other half start at the end of the data and work towards the beginning at the same pace! 

Consider the diagram below:

<img src='bidir_rnn.png'>

All of the boxes in black are the unrolled time steps that we would expect to see in a vanilla RNN for a model that tackles something like Named Entity Recognition. The first box is the model starting with the word 'He' and making a prediction, the second is the model making a prediction on the word "She", and so on. The red boxes are where things get interesting.  Whereas the black boxes should be read from left to right (as denoted by the direction of the black arrows connecting each time step), the red boxes should be read from right to left.  During time step 1, the first (left-most) black box is making a prediction on the word "He", while the first (right-most) red box is making a prediction on the word "president". When the model moves onto time step 2, the next black box represents the model making a prediction on "said", while the the next red box makes a prediction on "favorite", and so on. Note that although all the red boxes are moving from right to left, this is still part of the forward propagation step! The model makes it's predictions by using a formula to combine both of the activations from the forward-in-time and the backward-in-time neurons at each time step--this is actually a hyperparameter that keras lets us set when creating bidirectional layers. Thus, our model will easily be able to get the task above correct and recognize "Teddy" as a Named Entity, because although the forward-in-time neurons won't know what to do with the word "Teddy" since they haven't seen the word "Roosevelt" yet, the backward-in-time neurons _have_ seen this word already, and thus know enough to make the correct prediction for the word "Teddy"!

### Pros and Cons

Bidirectional RNNs excel at things like speech recognition and other NLP tasks. Typically, Bidirectional RNN Layers combined with LSTM cells are a great first place to start when tackling NLP tasks. However, they do come with the drawback of increased complexity and computational requirements, since each bidirectional layer is essentially double the size, since an equal amount of neurons are needed for each direction. This means that if we create a bidirectional layer of 50 LSTM neurons, then our model actually has 100 LSTM cells for that layer--50 for front-to-back, and 50 for back-to-front. This size increase can definitely slow down training times, because using things like LSTM cells are already quite time intensive. However, when it comes to performance with things like human speech, bidirectional models are often best-in-class!

## Using Bidirectional Layers in Our Models

Like all things, Keras makes it really simple for us to include a bidirectional layer in our model. Consider the following code snippet from the [Keras Documentation on Bidirectional Layers](https://keras.io/layers/wrappers/):

```python
from keras.layers import LSTM, Dense, Bidirectional
from keras.models import Sequential

model = Sequential()
model.add(Bidirectional(LSTM(10, return_sequences=True),
                        input_shape=(5, 10)))
model.add(Bidirectional(LSTM(10)))
model.add(Dense(5))
model.add(Activation('softmax'))
model.compile(loss='categorical_crossentropy', optimizer='rmsprop')
```

As we can see from the code above, to include a Bidirectional Layer, all we need to do is add a `Bidirectional()` object, which we just wrap around the layer that we want to be bidirectional! 

In the next lab, we'll put this newfound knowledge of bidirectional layers to use to build a model that can identify toxic comments and hate speech from real-world comment data sourced from Wikipedia!

## Summary

In this lesson, we learned about **_Bidirectional RNNs_** and the types of problems they are best suited for!
