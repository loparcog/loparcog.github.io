---
title: "Silly Sounds Update 2.1 - Polyphony \U0001F3B9"
summary: "I also made things *prettier*, if you could imagine"
date: 2026-04-08
draft: false
tags: ["synth", "vcv rack"]
showToc: true
TocOpen: false
ShowReadingTime: true
ShowBreadCrumbs: true
UseHugoToc: true
---

*As usual, all of the code for this project can be found at the [Silly Sounds Github repo](https://github.com/loparcog/SillySounds)*

## Polyphony and You

Did you know that VCV Rack has [single cable polyphony support](https://vcvrack.com/manual/PluginGuide#Polyphony)? I sure didn't! For some reason, I thought polyphony mainly worked through overlapping cables going to a single input, but it turns out a single I/O cable can have up to 16 channels running through it at a given time. After the linked required reading, provided by a lovely Github (and assumed VCV Rack) user through my issue board, I implemented polyphony into each existing module.

### Sesame

Sesame had an interesting time in all this. As a swing module, there didn't seem to be much value in adding clock signal or swing modification polyphony, as things could likely get really messy really fast, and I don't think anyone would have a fun time managing that. But, as a *signal repeater*, we could get a little funky. The polyphony count depends on the maximum channels into either the repeat trigger input or repeat modifier input, which will correspond to which output will have a repeated clock signal and by how much.

This way, all channels will always get the same clock signal with the same amount of swing, and you can choose which channels repeat when, and how many repetitions they each do.

### Lola

Lola was a little easier, since it is just a signal recorder and replayer. Only change now is she can take polyphonic input and output that same polyphonic data. 

### Kyle

Kyle is also quite simple, now specific channels can cause dips depending on the polyphonic input. It has essentially expanded to act on up to 16 signals sequentially instead of just one. If the old functionality is required, you would just need to combine your signal into one channel before feeding it to Kyle.

## Panel Visual Updates

This is definitely a more minor change, but I am big on aesthetics so I am very proud of making Sesame less ugly. Overall changes listed below:

### Sesame

![New and improved Sesame module](/img/2604sesamenew.png "Old Sesame module on the left, new Sesame module on the right, now with ASCII cats")

All I/O and parameters are the exact same, I just shifted her to a *vertical* orientation and switched some labelling from `repeat` to `dupe` to make the new form factor work. 

### Night Mode

![Silly Sounds modules in night mode](/img/2604dark.png "Sleek gunmetal, or whatever you want to imagine")

**DARKNESS**. I use night mode in almost all software I interact with, but for some reason the silver chrome of VCV Rack has never bothered me. Nevertheless, I saw this was an option and would like to extend an olive branch to my night panel lurkers. Please enjoy.

## End Notes

I'm hoping to make some chaotic modules in the near future, so look out for those! For now, feel free to drop a line in my [GitHub issue board](https://github.com/loparcog/SillySounds/issues) if there's any changes or modules you'd like to see.

*Toodles!*