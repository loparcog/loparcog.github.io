---
title: "Silly Sound's Lola \U0001F436"
summary: "VCV Rack eurorack module for live signal sampling and playback"
date: 2022-12-15T15:19:48-05:00
draft: false
tags: ["synth", "vcv rack"]
showToc: true
TocOpen: false
ShowReadingTime: true
ShowBreadCrumbs: true
UseHugoToc: true
---

*Feel free to check out this and other Silly Sound modules at [the GitHub codebase](https://github.com/loparcog/SillySounds) (they're well commented I swear). All of the code mentioned in this article can be found in [src/Lola.cpp](https://github.com/loparcog/SillySounds/blob/master/src/Lola.cpp)*

*Also if you are wondering, Silly Sounds is a mock Eurorack module company that these plugins will be published under. The company has and will never have profits, but is also a fake company, so we are planned to meet our goals year over year.*

## What is it

**Lola** is the second VCV rack module from Silly Sounds, and far simpler than the first. Also, far sampler. Ha.

Lola is an inline sampler for your Eurorack studio. Initially, it will act as a passthrough, directly passing any input signal as output. When there is something you want to record for playback, you can press the `rec` button or give a pulse input to start recording. Press the button again or give another input to stop recording. You can record up to 4 seconds because that's what I set it to.

Once a recording is stored, you can press the `play` button or give a pulse input to start playback of that signal. You can do this as many times as you want to keep replaying the sample. Pressing the `stop` button will immediately halt playback, making the module go back to its original passthrough mode.

![Lola module in VCV Rack](/img/lola.png "Lola module in VCV Rack. Bask.")

## How does it work

As mentioned, Lola is a very simple module. A vector is being used to store data at a given time, and an iterator is used to play back those voltage values when requested. Each set of controls (record, play, stop) all work off of a conditional check to turn flags on or off.

```cpp
// Check if the button or input trigger has been activated
// using a Schmitt Trigger
if (currentButtonValue > oldButtonValue ||
    SchmittTrigger.process(signalInput))
{
    // If flag not set
    if (!flag)
    {
        // Flip it and do any other misc actions
        flag = true;
        // Set lights, clear sample, reset iterator, etc.
    }
    // If flag is set
    else
    {
        // Flip it
        flag = false;
    }
}

// Save the current button value
oldButtonValue = currentButtonValue
```

Recording is a simple process of getting the current input voltage and using `push_back` to put it in a vector. This is done until recording is stopped manually or we reach the sample limit of 4 seconds. Likewise, output just uses an iterator to go through the sample, also ending when requested or when we reach the end of the sample. In any case, the module is either playing a passthrough signal or playing a sample.

## Conclusion and considerations

This was a good jump into the basis of storing and recreating sounds in VCV Rack. While writing this, I realize that some functionality to edit where to start and stop the sample would also be useful, as well as possibly storing multiple samples. Also, the ability to stop passthrough could be useful if trying to tighten up a setup to only use this as a sample player without needing to unplug input. I think this is something I would definitely like to come back to in the future to refresh and append to.

As it stands, it works! And honestly, it's what I would consider one of the better-looking modules right now.