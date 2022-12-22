---
title: "Envelope Detector Research and Ramblings \u2709\uFE0F"
summary: "Looking into different envelope detecting methods for a future sidechaining module"
date: 2022-12-19
draft: false
tags: ["synth", "research"]
showToc: true
TocOpen: false
ShowReadingTime: true
ShowBreadCrumbs: true
UseHugoToc: true
---

*This is all working off of a basis of what I know and understand, so take things with a grain of salt!*

## The problem at hand

I'm hoping to make a new module in VCV Rack (currently named Kyle) to act as a [sidechaining](https://en.wikipedia.org/wiki/Dynamic_range_compression#Side-chaining) module. Sidechaining is the technology that allows for music to automatically become quieter when a DJ is talking, or for other instruments to "step to the side" and quiet down each time the bass drum comes in to give it extra "oomph". Typically, this can be done by creating an [envelope](https://en.wikipedia.org/wiki/Envelope_(music)) for a signal, and passing an inverse that as the level to another signal, to move the volume of the latter down when the former is up. 

This is admittedly *extremely easy*, but I wanted to focus on more abstract sounds. This would focus on signals where an envelope cannot be easily defined and/or signals where I am too lazy to create an envelope for. This is where an envelope detector can come in handy.

## What is an envelope detector

An [envelope detector](https://en.wikipedia.org/wiki/Envelope_detector) is a tool that will map the envelope of a given waveform. The exact version of this would be an "analytic signal", but this tool and its multiple iterations aim to replicate that analytic signal as accurately as possible.

![Envelope of a signal example](/img/2212env.png "Example envelope from a signal, marked in red")

There are a few approaches to getting this result, each with their own pros and cons. The most important trait for my implementation would be reactivity, followed of course by accuracy. Any substantial delay would ruin the use of the sidechain envelope, so even with accuracy the result would be unusable.

## How can it be implemented

These are a few that I found while researching:

### Diode detector

This is the simplest implementation, and probably the easiest to understand. A waveform is passed through a capacitor in a simple circuit, which charges whenever there is a high in the signal, and drains whenever there is a low. This allows it to create an almost sawtooth wave covering the envelope of a wave. 

![Example implementation of a diode detector](/img/2212diode.png "Example implementation of a diode detector, and its downfalls")

As seen above though, some issues arise in its implementation. The aggression of the fall in the created envelope depends on how fast the capacitor loses charge, so if it doesn't lose charge fast enough, as seen above, there may be a large gap between the waveform dropping and the envelope following. This is still quite useful to consider, since it is easy to implement and reacts to rises immediately, so it may be usable given some modifications.

### Rectifier and low-pass filter

This is where things get not-so-simple. Both this and the following method dive into some actual signal modulation to create an envelope from the original signal. I am also not the most knowledgable in this so please refer to this article's header. From this point on, we get into signals. Most of the base information I got was from [this MathWorks link](https://www.mathworks.com/help/dsp/ug/envelope-detection.html). Beware.

This is the basic chain of this operation:

$$\text{Input} \rightarrow u^2 \rightarrow u*2 \rightarrow \text{Downsample} \rightarrow \text{LPF} \rightarrow \sqrt{u} \rightarrow \text{Output}$$

The **input** to this is the original sound waveform. This signal is **rectified** (or squared), to essentially make it its own carrier wave. [Rectifying](https://en.wikipedia.org/wiki/Rectifier), for our understanding, mainly takes the loud sound wave and brings it back to a much smoother wave. From there, the signal is **multiplied by 2** to help scale it up for processing, and **downsampled**. [Downsampling](https://en.wikipedia.org/wiki/Downsampling_(signal_processing)) removes some sampled data points, to simulate a lower sample rate and reduce the complexity of our resulting wave. Finally, a LPF, or **Low Pass Filter** is applied. A [Low Pass Filter](https://en.wikipedia.org/wiki/Low-pass_filter) allows for low frequency waves, like our resulting wave, to pass through, and prevents high-frequency waves, like any small fast bumps in that wave, to be *smoothed out*. Finally, we **take the square root** of the resulting signal to scale it back down and match it to our original data. 

This process definitely take some steps up in complexity, but it is really interesting on how it modulates the original waveform. Doing downsampling and (mainly) filtering in real time can sometimes be an issue in terms of delay, but this is something we can investigate.
Essentially, this is a lot more accurate than our previous method, but a lot more expensive.

### Hilbert Transform

I am going to save the deepdive on this. This is something you can check out on the [original MathWorks link](https://www.mathworks.com/help/dsp/ug/envelope-detection.html), but it is essentially a more grandiose version of our rectifier and LPF described above. This one is seriously impressive, but again, is more computationally expensive. The main problem is that a [real time Hilbert Transform](https://www.nature.com/articles/s41598-021-97560-5) is a point of high-level research, and as well that its main focus is on waveforms without massive changes. This does not match our use case.

Really cool though.

## What I would like to do

As mentioned above, each piece has their pros and cons. The main things I want to keep in mind though are the following:

- This is for a real-time sidechaining module, so delay would be easily noticeable, and the ideal case would be if we could even be *early*.
- This will be a VCV Rack module at the end of the day, so we will be sharing computer resources with a wide range of other modules. Something computationally expensive may be neat in its own physical module, but would be rude in this context.

With these in mind, I would mainly want to focus on an *advanced* version of the diode detector, maybe with some peak detection so we could be aware of jumps from high to low frequencies. The best way to discover what's best would be through testing though, so that's what's next currently.



