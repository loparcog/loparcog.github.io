---
title: "Silly Sounds - An Introduction \U0001F39B"
summary: "The background for all other content under the Silly Sounds real fake label"
date: 2022-11-15
draft: false
tags: ["synth", "vcv rack"]
showToc: true
TocOpen: false
ShowReadingTime: true
ShowBreadCrumbs: true
UseHugoToc: true
---

## Welcome to my fake company!

Silly Sounds is an amalgamation of my wants to code, market, and do something ambiguously musical. Making a fake eurorack development company seemed to be the best way to do that. Most of my projects under this label will be looking into different audio programming concepts I come across while I make any eurorack modules that I think would be fun to have. I'm sort of getting ahead of myself here, let me lay out some background first.

## Background

If you're familiar with VCV Rack you can probably skip ahead. Otherwise, here is a crash course for Eurorack as I understand it. Synthesizers commonly have multiple parts working in unison including something to make sound (commonly waveforms), something to filter that sound to add some *depth*, something to keep track of time and possibly sequence notes, and many, many more. All of these tools are implicitly part of the synthesizer though, which allow different parameters to be altered but always function and communicate in specific ways. Somewhere in the 50s to 60s someone moved towards making a more "modular" system, something that can have standard voltage inputs and outputs for each part of the synthesizer so you can customize exactly how different components communicate and are controlled. This has spiralled since then, and dawned the birth of Eurorack in the 90s. 

![A set of Eurorack modules](/img/eurorack.jpg "An example of a healthy-sized Eurorack case, with each block of knobs and plugs being their own module")

Eurorack takes modular synthesizers one step further, and breaks components down into their own panels. Now users can pick and choose which modules they want to use in their setup, and since things are standardized, they can really combine any module from any manufacturer. This birthed a lot of really interesting sounds and equally confusing and expensive setups, which I am both excited and fearful to invest in. Thankfully, there are less costly options to play with modular synthesis, one of those being virtual synthesizers. There are many types of virtual synthesizers, but the one Silly Sounds modules are made for is [VCV Rack](vcvrack.com/). This open-source beauty is free to use and also has massive developer support, allowing anyone to create virtual modules and submit them to be on the official module library. I am heavily invested in audio programming right now, and as someone with minimal signal processing experience, I thought this would be a great place to start.

## Current and Future Modules

Any module code can be found at [my Silly Sounds GitHub repo](https://github.com/loparcog/SillySounds), with each having their own writeup here on how they work and how they were made. As it stands there are three modules

- [Sesame]({{<ref "2211sesame.md">}}): A clock modulator to allow for clock swing and signal burst, as well as the use of both simultaneously
- [Lola]({{<ref "2212lola.md">}}): A live sampler to take input and repeat it at any given time
- [Kyle]({{<ref "2301kyle.md">}}): A sidechaining module utilizing envelope detection to dampen other signals when a given signal amplifies

