---
title: "The Game of Disconnecting \U0001F50C"
summary: "My first dive into actual site and service hosting through disconne.cc"
date: 2023-05-16T13:58:52-04:00
draft: false
tags: ["code", "webdev"]
showToc: true
TocOpen: false
ShowReadingTime: true
ShowBreadCrumbs: true
UseHugoToc: true
---

*This site has since been taken down, and is looking to be replaced by whatever other web project pops into my mind*

## What is it

**DISCOnne.cc** was a game project I had sitting around for a long time. It had originally been set up and sitting around in some form since August 2021. At this point, I was pretty deep into browser games (flash games are still near and dear to my heart, but in this case, think things like [slither.io](http://slither.io/), [vertix.io](http://vertix.io/), etc.), and was interested in dipping my toes in. As everyone is probably very familiar with at this point, these games all have some commonalities between them:

- A sense of progression and growth the longer you're in the game
- A basic gameplay loop to allow anyone to get in and quickly get involved
- A quick game cycle, getting people bounced out of the game and back in as fast and commonly as possible

I was looking to hit all of these notes in the simplest and stupidest ways possible. Some quick battle games like rock paper scissors came to mind, but that still had too much structure. I wanted something with a healthy dose of RNG and a nonsensical gameplay loop. I decided to have a game where people would not be able to directly attack others, but they would be able to attack and instantly defeat other players. And since I didn't have any real ad space to show on the home page or any reason to pull them back, I thought it would be funny to just fully disconnect players when they lost.

And it was (at least to me)!

## Why did you do this

I had some free time and wanted to see what putting out a website would be like. I also wanted to introduce chaos into the world. I don't think I need or genuinely have more reason.

## How does it work

This was all run on a Node.js server hosted on Google Cloud Platform using their free trial credits because why not. All of the networking was done on a basis of socket.io, making the interactions between players and the subsequent disconnections very easy. The frontend was just basic HTML and CSS since making a fancy React frontend for this felt disingenuous. *And also would have probably been the same amount of work as putting the backend together.*

This all works off of the basis of websocket connections. When you connect to the game, you are put into the mix of everyone else currently playing. The server tracks you by your socket UID, your total time in the game, and your available time to *spend*. Time to *spend* can be used on disconnecting another user or parrying a disconnect for a cost of 10s or 5s, respectively. These are both untargeted actions. Your time to spend is essentially your total time that is subtracted from every time you perform an action.

When you disconnect another user, the server will randomly choose a currently connected user. The server will then check if that user is viable to be disconnected. If the user to be disconnected has used a parry action in the last 2 seconds, then they will actually reflect the disconnect and **disconnect you instead**. Stupid, right?

This parry windy will also be open for 5 seconds whenever a player joins the game, just to make sure they don't get immediately booted from the game and to add some *flavour*. If the user has been in the game for more than 5 seconds though, and their parry is not active, then they will be disconnected.

Obviously the main issue here would now be balancing. The cost of disconnecting would mainly dictate the speed of the game, and the parry action would need to work around that. For this, I made sure that when a user parries, they cannot parry for another 5 seconds, or the original cost of the parry. This will make them invulnerable to disconnects for 2 seconds, and then vulnerable and on parry cooldown for another 3 seconds, to prevent parry spamming.

This *defending from an invisible threat* and the opposite of *attacking any random player* was extremely fun to implement, and is something I would love to explore in a more complicated game setup as well. Of course, these would likely not be *regular game mechanics*, or rather, something that I think users en masse would love to see implemented across all games. I just think the silliness of it all would be attractive for a bit to people who are tired of the usual strategic and brain-hurting approaches.

## What was the setup

Setup was actually a breeze. The hardest part was choosing a domain. I got the beauty of disconne.cc for around $10/year, and navigated through Google Cloud Platform (GCP) to host. I've played around in GCP from my current position in Darktrace, but never actually deployed anything personally. For a small passion project, and seeing as they were offering a bunch of free starting credits for 90 days, I thought why not.

The site was a quick and simple upload of code, upload of a boot script, and we were off to the races. That being said, this barely scratched into the free credit offerings of GCP, $3 to be exact, so I may try to take advantage of that in the future, or I recommend others do too.

## Where were the bugs

~~There were none! There never are!~~

Immediately diving into webdev with user to user interactions would obviously come with some unforeseen problems. The first one I saw was actually when I tweeted out the link to the site. 

Traditionally, when you close the browser window, socket.io would detect this and mark you as disconnected. However, if you accessed the site through a built-in browser like the one found in Twitter, leaving the page would not mark you as disconnected. You could stay there perpetually as long as someone else didn't disconnect you, and opening the page again would treat you as a whole other user. This was something that could have been solved by an inactivity monitor, but that would have also come with its own host of problems.

Furthermore, later in its deployment, the server started to state that the time people had stayed on the site was 10 seconds less than their actual time, leading the timer to start off at `-10`. This one was extra confusing to me, and there must have been some sort of bug in the logic on how I computed the time users joined from the client and server side that I never caught in testing.

I imagine if this was still running, more bugs would be coming up that would need to be squashed, but I'm hoping to replace this now with something a bit more *basic* on the server-side, leaning a bit more into client-side experiences without the need for server-side validation and user-to-user interaction.
