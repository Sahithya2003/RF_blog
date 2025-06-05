---
layout: single
title: "I Built a Beam-Steering Antenna (and It Actually Worked)"
date: 2025-06-05
permalink: /phasedarrays
author_profile: true
read_time: true
toc: true
math: true  
---

<style>
body {
  font-size: 15px;
  line-height: 1.8;
}
h1 { font-size: 2.2em; }
h2 { font-size: 1.9em; }
h3 { font-size: 1.6em; }
</style>

This project started like most of my RF explorations: with curiosity, a bit of caffeine, and a well-written research paper. The title? *Electronically Controllable Varactor Loaded Beam Switchable 4-Element Array Antenna for Sub-6 GHz Applications*. The idea? An antenna array that could steer its beam without physically moving a single part — by embedding varactor and PIN diodes directly into the feed network to shape the phase front.

At its heart, the design operates around 6.2 GHz and blends two clever tricks: using PIN diodes to steer the beam to fixed angles (like +30° or −30°), and varactor diodes to fine-tune the direction. It’s efficient, practical, and very relevant to what we need for modern wireless systems — think 5G, radar, or anything where you need to talk to different places at different times without moving your hardware.

### Why Bother Steering Beams Electronically?

Most antennas radiate energy in all directions. While that’s great for general coverage, it’s not ideal when you want focused communication. Beam steering gives us that control — we can direct energy precisely where it’s needed. That means better range, less interference, and smarter power use.

This particular design solves beam steering using a clever mix of simplicity and precision. It avoids bulky moving parts or expensive active phase shifters. Instead, it blends PIN and varactor diodes into a feed structure that controls the phase delay by toggling the signal path length — introducing shifts that steer the beam. That makes it elegant and scalable.

I set out to replicate their antenna, not only to test their claims but also to peel back each layer — to understand what each component does and how it helps bend the beam.

### Peeling the Layers: Why This Antenna Looks the Way It Does
![peeling1](/RF_blog/assets/images/peeling.png)  
*Figure 1.* Exploded view of the stacked antenna showing patch layer, aperture-coupled ground plane, and feed network with phase shifters.

The antenna uses a stacked design, with two layers of FR4 substrate (each 1.6 mm thick), separated by a ground plane. On top: four square radiating patches. Below: the feeding network with diodes and lines. In the middle: precisely cut slots that couple energy from the feed to the patches.

This layout gives you freedom. You can tweak the feed network without disturbing the radiator, and vice versa. Plus, it avoids soldering headaches and helps suppress unwanted noise.

This isn’t just for aesthetics or space-saving. Here’s why it matters:

- Aperture coupling (via the slots) allows energy to jump from the feed network to the patches without vias, which avoids messy soldering and parasitics.
- Separation of feed and radiator reduces mutual interference and makes tuning easier.
- More control: Each layer can be optimized independently — ideal when you’re embedding varactors and PIN diodes into the feed.
- Cleaner patterns: Isolating the feed prevents it from radiating unwanted energy.

Put simply, the stacked design gives you flexibility, control, and cleaner radiation. And this separation also lowers parasitic coupling and eases impedance tuning across layers.

![array](/RF_blog/assets/images/array.png)  
*Figure 2.* HFSS model of the full array structure with radiation box and stacked substrate layers.

You’ll notice these slots aren’t random — they’re precisely shaped and placed to direct energy efficiently. This matters a lot when you’re dealing with phase-sensitive beamforming.

### The Diodes: Where the Real Steering Happens

Now let’s talk about the heart of the beam steering: the PIN and varactor diodes. Each plays a distinct role.

- **PIN diodes** — act as RF switches  
- **Varactor diodes** — act as voltage-controlled capacitors

#### How Many, Where, and Why?

- **24 PIN diodes**: These are grouped into six switch-line phase shifters (four for 90° phase shifts, two for 180°). Each shifter has two diodes that steer the signal through either a short or a long path, introducing the desired phase difference.  
  (Basically when the PIN diode closes the longer delay line, the RF signal picks up ~90° extra phase due to increased path length. It’s this delay differential, not just the diode state, that bends the beam. Each switch-line structure mimics a 1-bit digital phase shifter: choosing between a short (0°) and long (~90° or 180°) path — enabling modular beam control.)

- **4 Varactor diodes**: Placed just before the feed point of each patch. By tuning their capacitance, they shift the phase continuously, giving us fine-grained control of beam direction.

This two-level control — discrete switching with PINs, continuous adjustment with varactors — is what makes this design both flexible and cost-effective.

*In the diagram below, the circles represent varactors, and the tiny square elements near phase shifters are the PIN diodes. Each arm of the feed network has a PIN-controlled switch-line phase shifter followed by a varactor.*

![back](/RF_blog/assets/images/back.png)  
*Figure 3.* Bottom view of the feeding network showing switch-line phase shifters and varactor placement before each patch input.

### In RF Terms...

In simulation (HFSS), diodes aren’t magical boxes. They’re modeled with RLC components to capture real behavior:

- **PIN diode (ON)**:  
  `R = 7.8 Ω`, `L = 30 pH`

- **PIN diode (OFF)**:  
  `C = 0.025 pF`, `L = 0.6 nH`, `R = 10 kΩ`

- **Varactors** are modeled as variable capacitors: the higher the voltage, the lower the capacitance, and the more the signal gets delayed. In HFSS, I manually swept the capacitance from 0.15 pF to 1.3 pF to simulate real tuning.

### Feeding the Signal: The Corporate Network

This design uses a **corporate feed network** — basically a tree structure that splits the incoming signal equally into four paths. But there’s more going on:

- The network uses alternating 50 Ω and 100 Ω lines to manage power distribution.
- **70.7 Ω quarter-wave transformers** sit between these sections to ensure impedance.

Why care? Because bad matching = signal reflections = trash performance.  
This part may look boring, but it’s where the real steering precision comes from.

### Simulating the Beam Steering

I set up three main simulation cases:
![odegreed](/RF_blog/assets/images/0e.png)  
*Figure 4.* Simulated E-field distribution for the 0° steering case (S0). Equal phase at all ports results in forward-facing main beam.
![26degreed](/RF_blog/assets/images/26e.png)  
*Figure 5.* Simulated E-field distribution for the +26° steering case (S3). Signal delay on one side introduces constructive phase shift, steering the beam right.
![30degrees](/RF_blog/assets/images/30e.png)  
*Figure 6.* Simulated E-field distribution for the −28° steering case (S4). Phase delay on the opposite side steers the beam to the left.

- **S0 (0°)** — balanced paths, no phase difference  
- **S3 (+30°)** — one side gets delayed using switch-lines  
- **S4 (−30°)** — other side gets the delay

#### Here’s what happened:

- In S0, the beam pointed straight forward.
- In S3, the beam shifted to around +26°.
- In S4, the beam went to about −28°.

Not quite ±30°, but close — and this is where it gets interesting.

### The Reflector: Quietly Doing a Lot

At the very bottom of this sandwich sits a **metal reflector**, spaced about 10.8 mm (or λ/5) below the bottom layer. 

**With reflector:**  
- Reflects backward radiation forward → more gain  
- Adds constructively → tighter beam  
- Suppresses back lobes → cleaner pattern
![rad_with_reflor2](/RF_blog/assets/images/radiation_r.png)  
*Figure 7.* Simulated radiation pattern with reflector. Forward gain improves and side lobes are suppressed due to constructive reflection.
![rad_with_reflor](/RF_blog/assets/images/r.png)  
*Figure 8.* Gain vs. theta plot with reflector. Forward gain is enhanced, and overall beam directivity improves with reduced side lobes.
![gain2](/RF_blog/assets/images/gain_r.png)  
*Figure 9.* 3D gain plot with reflector. Peak gain increases to 4.43 dB, with a tighter and more forward-focused beam compared to the case without a reflector.

Didn’t think much of it... until I removed it. Oof.

**Without reflector:**  
- Side lobes spike  
- Gain drops by nearly 1.5 dB
  
![rad_without_reflor](/RF_blog/assets/images/radiation_wr.png)  
*Figure 10.* Simulated radiation pattern without the reflector. Beam is distorted with prominent side lobes and reduced forward gain.
![rad_without_reflor2](/RF_blog/assets/images/wr.png)  
*Figure 11.* Gain vs. theta plot without reflector. The main lobe is weakened and multiple side lobes appear due to lack of constructive reflection.
![gain](/RF_blog/assets/images/gain_wr.png)  
*Figure 12.* 3D gain plot of the antenna array showing directional radiation with a peak gain of 3.26 dB at 6.2 GHz. Red lobe indicates the main beam direction.

Why? It reflects energy back into the beam and reduces back radiation. Quiet. Effective. Absolutely necessary.

**Conclusion:** Quiet. Effective. Absolutely necessary.

### Why My Results Don’t Exactly Match the Paper
![rad_26](/RF_blog/assets/images/26.png)  
*Figure 13.* Radiation pattern for +26° beam steering. 
![rad_30](/RF_blog/assets/images/26.png)  
*Figure 13.* Radiation pattern for +30° beam steering. 
This part matters.

- **Mutual coupling**: The patches are close together. They interact.
- **Finite ground plane**: In theory, it’s infinite. In HFSS, it’s not.
- **FR4 losses**: Real materials aren’t perfect.
- **Static diode modeling**: I used ideal switches, not full nonlinear models.
- **Reflector spacing**: λ/5 is sensitive. A mm off can mess things up.

PS: Yeah, I placed my antenna the other way around — so in all my polar plots, what should’ve been 0° shows up at 180°, and +30° shows up at 150°. But you get it right?  180 − 180 = 0°, and 180 − 150 = 30°. Math still maths. Beam still beams. We move.

### What These Results Show

Even with all that — the beam moves. The gain behaves. The back lobe drops. The design holds up.

This wasn’t just about replication. It was about **understanding**. And now, if you came here hating RF, you just watched a beam bend without a single motor.

Admit it — that’s pretty badass.

