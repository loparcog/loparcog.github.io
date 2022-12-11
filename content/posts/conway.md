---
title: "Conway's Game of Synth \U0001F47E"
summary: "Merging an interest in Conway's Game of Life with very crude generative synthesis in a small side project."
date: 2022-01-31
draft: false
tags: ["synth"]
showToc: true
TocOpen: false
ShowReadingTime: true
ShowBreadCrumbs: true
UseHugoToc: true
---

*This project is currently unhosted, and any code referenced can be found at [the GitHub codebase](https://github.com/loparcog/conway-synth)*

## What?

*What in the good name of Conway is a Conway Synthesizer*

Okay so, this one is going to come in layers. First off, Conway's Game of Life. John Conway, a British Mathematician created the Game of Life in 1970, with the wiki [found here](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life) and a playable demo [found here](https://playgameoflife.com/). Both of those links would explain it better than I could, but essentially, it's a zero-player game, setting a starting state and then letting the game run its course by deciding which cells will be on or off in each following iteration. The rules for which cells live and die each iteration can be condensed to the following:

1. Any live cell with two or three live neighbours survives, having a healthy surrounding population.
2. Any dead cell with three live neighbours becomes a live cell.
3. All other live cells die in the next generation due to over or underpopulation. Similarly, all other dead cells stay dead.

This in itself isn't anything to ride home about, *but*, the patterns people have found for this game have been, personally, mind boggling.

![Example pattern in Conway's Game of Life](/img/gol.gif "Example pattern in Conway's Game of Life")


A synthesizer is a synthesizer I'm gonna leave that rabbit hole for you to dive into at your own discretion. Give it a signal it'll make a sound, which can be modulated to be light and clean or extremely harsh and aggressive.

## Why?

*...*

The main drive behind this one is wanting to get something *musical* out, but not being able to force myself to sit down at the piano and try to learn chords. I know how to code, so I thought this would be nice, and generative art is all the rage. Conway's Game of Life has always intrigued me, and with such a simple backing, I thought it would be really interesting to try to add some musical aspect to it. This has been done before by others, [as](https://www.youtube.com/watch?v=x22zysfrVSk) [seen](https://www.youtube.com/watch?v=zbVOK2GLV-E) [here](https://people.ece.cornell.edu/land/courses/ece5760/FinalProjects/f2011/lba36_wl336/lba36_wl336/index.html), but many of these follow a step-sequencer approach, treating the y-axis as pitch and x-axis as time. What I wanted to do was get something that truly generated sound with each iteration, more like [this project](https://www.youtube.com/watch?v=avK-BmL2KZ4), but with more chance for user modulation and adjustment. Thus, the *synthesizer* part of the Conway Synthesizer. I also really wanted to utilize Tone.js after looking at it for past projects, and thought this would be a great use case. The project isn't about the music I can make but rather the musical tools I can create for others, implementing the built up knowledge of Game of Life and applying it to a musical landscape.

If the why is pertaining to why I made a blogpost about this, I got bored of coding get off my back.

## How?

*So x-axis is pitch, y-axis is...*

This is the point where this document is very much alive, so take things here with fistfulls of salt. The overarching background for this is that everything is coded in javascript, with the synth sounds coming from the very lovely [Tone.js Library](https://tonejs.github.io/)

The Game of Life how is very simple. As stated above, there's those three rules for how cells should be created or destroyed. For this, I'm using a canvas to create a grid of rectangles which can be toggled on and off, and when started, will continue to be toggled based off those rules each iteration. Small data structure for each cell so they have code on what to do when clicked and where they are on the canvas as well as in relation to other cells, index-wise. Easy peasy.

The ***SYNTHESIZER*** though, this is interesting. So, we have two axis to play around with. As I'm most familiar with piano, it seemed to make the most sense to make the x-axis change the pitch. Again, easy peasy, just set a number-to-key translation, which [Tone nicely does](https://tonejs.github.io/docs/r11/Frequency), and set each cell to have it's own note based on it's x-axis index. Iterating the game now makes a mash of sounds, and the gliders sound like someone slamming their fist on the piano in lower at lower and lower points, or higher, however you set up your glider. To avoid some of this chaos, I removed sounds from cells that stay alive between iterations, and only set it to play sounds from newly generated cells. Still sounds like chaos, but much less so.

Before going into ironing out the x-axis, there is a giant design issue here. The y-axis. What does it represent? What sound aspect can be changed? What does Tone allow? All great questions, none too helpful. I've currently still been playing around with some options, specifically things like volume, timbre, or oscillator functions like waveform and phase. As it stands, I still need to do some experimentation to see what would be best left to the Game of Life and what would be best left to the user, but I'm currently leaning towards the waveform shape being adjusted on the y-axis. The envelope would be a little messy since that's like four different parameters, so I'm leaving that one to the user. Something else I've been looking at was the [Roli Seaboard](https://roli.com/products/seaboard), which has "five dimensions of touch control", and essentially uses where you're pressing on the key as another way to modulate the sound. It's an interesting problem that I've left for now.

Now, ironing out the x-axis. Scrolling through all possible notes, seeing as how the Game of Life will commonly need cells to generate close to one another, doesn't seem like the best solution to make things sound "good". I've though about playing around with scales instead, letting the user decide on which scales they may want to set the game to use, and then try an iteration and see how it sounds. Other options were maybe setting up chords, but for mass-generating machines, that would also sound probably too chaotic to be good. In the example above, setting portamento and release pretty high to get that spacey effect on all the notes would be a great way to merge them, but those would be parameters I would want to leave for the user, so that's not an option.