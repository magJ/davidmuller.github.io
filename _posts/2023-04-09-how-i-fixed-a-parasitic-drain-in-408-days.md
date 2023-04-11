---
layout: post
title: How I Fixed a Parasitic Drain on my Car in 408 Days
date:  2023-04-09 18:00:00 -0700
categories: posts
---

# {{ page.title }}

<small style="font-weight: 175;">{{ page.date | date: "%-d %B, %Y"}}</small>

I almost lost my mind fixing a persistent battery drain on my 2013 Mazda3 hatchback. I want to share how I fixed my car in case it helps you fix a similar problem.

> **Short version: I manually disconnected the key fob computer system to stop the battery from draining.**

## Finding the Parasitic Drain

My car battery would drain itself in a matter of hours or days starting in January 2022 until I fixed the issue in March 2023. This kind of unchecked battery drain---where your car does not have enough power to start itself after its parked for a short time---is sometimes called a parasitic drain.

To diagnose the drain, I parked and locked my car. Then, I [measured voltages across some of the exposed fuses in my car](https://m.roadkillcustoms.com/how-to-perform-a-parasitic-draw-test/). The idea is to find components in the car that are drawing power when the car is off. A few fuses showed constant power consumption including the `TCM Transaxle control system` fuse:

<img style="max-height: 552px" src="/images/mazda3-2013-tcm-transaxle-control-system-fuse-draw.jpeg" alt="mazda3-2013-tcm-transaxle-control-system-fuse-draw.jpeg"/>

Using [a lookup table for my brand of fuses](https://m.roadkillcustoms.com/wp-content/technical-pdf/Mini%20Fuse%20Voltage%20Drop%20Chart.pdf), I converted the `0.9mV` voltage on `TCM` into a current draw. My car drew significant current over four fuses even when it was off:

| Fuse    | Description                          | Compartment                                                | Current            |
|---------|--------------------------------------|------------------------------------------------------------|--------------------|
| `TCM`   | 15A - Transaxle control system       | [engine](/images/mazda3-2013-engine-compartment-fuses.pdf) | `0.9mV` -> `197mA` |
| `ENG+B` | 10A - Engine Control System          | [engine](/images/mazda3-2013-engine-compartment-fuses.pdf) | `0.2mV` -> `27mA`  |
| `ROOM`  | 15A - Overhead lights                | [engine](/images/mazda3-2013-engine-compartment-fuses.pdf) | `0.4mV` -> `87mA`  |
| `ESCL`  | 15A - [Electric Steering Wheel Lock] | [interior left](/images/mazda3-2013-left-side-fuses.pdf)   | `0.4mV` -> `87mA`  |

According to one mechanic who worked on my car, the total draw on my 2013 Mazda3 should be `50mA` or less when the car is parked and locked. So, the `197mA` to the transmission computer (`TCM`) was especially troubling. Why would the transmission computer remain on when the car was parked and locked, anyways?

When my car fist started dying in Februrary 2022, the key fobs started malfunctioning. Sometimes when I tried to use a fob, the car would sound warning chirps for ~10 seconds. Paranoid the key fobs were causing the drain, I hadn't used the fobs since the car first died. I had removed the batteries from the fobs, and only used my metal backup key.

Now over a year later, I guessed that the keyless system was still malfunctioning even though I wasn't using the fobs. Perhaps, the car thought a key fob was present in the car, and it was keeping systems like its transmission computer (`TCM`) switched on endlessly by mistake.

## The Fix

One of the [keyless troubleshooting sections in the workshop manual](/images/mazda3-2013-advanced-keyless-entry-push-button-start-ENG+B-ESCL-ROOM-fuse.pdf
) mentioned the `ENG+B`, `ROOM`, and `ESCL` fuses could be related to keyless system malfunctions. Since I'd also seen power going over thoses fuses, it seemed plausible that the keyless system was still acting up.

I was already only using my metal key and not the fobs, so I had nothing to lose. I opened up the dash behind the glove compartment to see if I could disable the key fob system directly. After disconnecting my car's negative battery cable, I unplugged [wiring harness 4](/images/mazda3-2013-keyless-control-module-wiring-harness.pdf) from the [keyless control module](/images/mazda3-2013-keyless-control-module-removal.pdf):

![mazda3-2013-keyless-control-module](/images/mazda3-2013-keyless-control-module.jpeg)

[Wiring harness 4](/images/mazda3-2013-keyless-control-module-wiring-harness.pdf) is almost exclusively communications lines to the different radio antennas and switches that comprise the keyless system.

After unplugging wiring harness 4, the instrument cluster now showed a red warning light that keyless system was malfunctioning (what I wanted):

![mazda3-2013-keyless-warning-light](/images/mazda3-2013-keyless-warning-light.jpeg)

When I drive the car, I see the red keyless system warning light. However, when I park the car and lock it, the `TCM` fuse (and the others) go to sleep and stop drawing power over the course of several minutes. My car no longer dies after a day or two of sitting. Success!

## FAQ

**Why did you almost lose your mind?**

- I suspect the drain was intermittent: sometimes the car would go to sleep properly, and sometimes it wouldn't. Intermittency made the problem hard to diagnose.
- Also, two real mechanics told me my car didn't have a parasitic draw.

**What did the real mechanics say?**

Different mechanics told me different stories:

- **Dealer:** said my car was fine and returned it to me. My car died the next morning.
- **Second mechanic:** said my battery was degraded, and low voltages from the battery were freaking out the keyless sytem.
    - Indeed, the car lasted 12 months problem free on a new battery before it started dying again.
    - (I still was only using my metal backup key, and had removed the batteries from the fobs.)
    - A second new battery did not fix the issue when the problem resurfaced after the 12 months.

**Why did it take so long to fix?**

I think it was because the problem was intermittent. See the full timeline at the end of this post.

**Is your car still safe to drive?**

As far as I can tell, yes. The [#4 cable I disconnected only makes connections to the keyless system](/images/mazda3-2013-keyless-control-module-wiring-harness.pdf), and the only warning light is a red one indicating the keyless system isn't working (expected). All other systems seem to be operating normally. You should probably consult an actual mechanic if you're considering this change, though.

**If the parasitic draw is intermittent, how do you know you fixed it?**

All the evidence I have points to the keyless system: key fobs malfunctioning, measuring the draws on the same fuses in both Feb 2022 and Mar 2023, and the [implication of some of the related fuses from the workshop manual](/images/mazda3-2013-advanced-keyless-entry-push-button-start-ENG+B-ESCL-ROOM-fuse.pdf
) that mentioned the `ENG+B`, `ROOM`, and `ESCL` fuses. My car also hasn't died again yet.

**What mistakes did you make troubleshooting the draw?**

Too many to count. Most important is to make sure your car is locked with the doors closed before you run any experiemnts to look for parasitic draws. Cars can take a while to go to sleep.

**Did you use a [diagnostic code reader](https://en.wikipedia.org/wiki/On-board_diagnostics) to look for errors logged by the car?**

No, I probably should have. Sometimes I wanted to pretend the problem didn't exist more than I wanted to fix it.

**Could you have disabled the keyless module in software?**

It looks like it's [possible to "disable" the keyless module in software](/images/mazda3-2013-keyless-control-module-configuration.pdf). It's possible this would have resolved the issue as well, but I don't know. Since I don't have Mazda's proprieratry M-MDS diagnostic tool, I couldn't disable the keyless module in software myself.

**How did you download the Mazda3 2013 workshop manual?**

I used [this site](https://www.allcarmanuals.com/factory-service-manual-346-Mazda-3-BL.html) for the workshop manual.

**What other parasitic drain posts did you read?**

I read lots of forum threads about parasitic drains. One common theme was faulty bluetooth modules:
- [Post about the bluetooth module causing a drain](https://www.mazdaforum.com/forum/mazda3-26/mazda-3-parasitic-drain-39452/page3/#post192957).
- ["Puzzling battery drain" - with another mention of bluetooth](https://www.mazdaforum.com/forum/mazda3-26/puzzling-battery-drain-48584/#post199744).
- There is also some discussion of parasitic draws [in the comments section of the hackernews post for this blog entry](https://news.ycombinator.com/item?id=35513636).

**How did you remove the glove compartment to access the keyless control module?**

Even though it's about replacing a blower motor, the [beginning of this video](https://www.youtube.com/watch?v=bS4gcumow3s) is a good guide for getting into the dash.

**Did you have help?**

Yes, a lot. Many thanks to my older brother, Steve, and my Dad.

**Problem Timeline:**

| Date     | Status                                                                                                                                         |
|----------|------------------------------------------------------------------------------------------------------------------------------------------------|
| Jan 31 2022 | Car starts dying.                                                                                                                              |
| Feb 2022 | Key fob is acting up.                                                                                                                          |
| Feb 2022 | Dealer says car has no parasitic draw.                                                                                                         |
| Feb 2022 | Car dies day after dealer returns it.                                                                                                          |
| Feb 2022 | Fully disassemble key fobs (remove batteries). Only use metal backup key.                                                                      |
| Feb 2022 | Self diagnose parasitic draw on `TCM` fuse (and others).                                                                                        |
| Feb 2022 | Get new mechanic.                                                                                                                              |
| Feb 2022 | New mechanic says I just need a new battery. "Low voltage from your degraded battery makes the keyless system malfunction and drain your car." |
| Feb 2022 | Get new battery.                                                                                                                               |
| ---      | Car stops dying for ~11 months. (Key fobs still disassembled. I'm only using metal backup key.)                                                               |
| Jan 2023 | Problem resurfaces.                                                                                                                            |
| Jan 2023 | Get another new battery. ("The cold winter temperature probably killed the battery").                                                          |
| Feb 2023 | Car continues to die overnight.                                                                                                                |
| Feb 2023 | "If I ignore the problem, it will go away."                                                                                                    |
| Mar 2023 | Observe same draw on `TCM` fuse (and others) that I observed previous year.                                                                    |
| Mar 15 2023 | Disconnect keyless system.                                                |
| Apr 9 2023  | Car starts fine after 10 days parked.
