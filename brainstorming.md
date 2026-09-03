# through-wall sensing - feasibility notes

## the honest split

- through-wall **motion detection** ("someone is moving in the next room"): yes, real, done for decades. doppler/UWB radar goes through drywall, plywood, brick fine.
- through-wall **2D tracking** (blip on a screen, range + rough angle of one moving person): ambitious but plausible for a strong team in a semester. this is the demo that packs the expo.
- through-wall **imaging** (silhouette / skeleton / pose): not realistic. MIT's RF-Pose is years of work + a trained neural net + synchronized camera ground-truth data collection + custom wideband array. don't promise this. can mention as "the dream" only.

## physics constraints that pick our parts

- walls attenuate more at higher freq. **60 GHz mmWave (TI IWR6843) basically does NOT go through walls.** rules out the easy off-the-shelf radar path.
- sweet spot for wall penetration + usable resolution: roughly **3-10 GHz**.
- UWB impulse radar in that band is legal under FCC Part 15 UWB mask (very low power, no license). building a punchy FMCW radar in a licensed band is not. stay in Part 15.
- range resolution needs bandwidth. angular resolution needs multiple antennas. imaging needs both, a lot. tracking one blip needs much less.

## candidate approaches

1. **buy a UWB impulse radar chip.** Novelda X4 / Xethru (now part of Asinger/Novelda), ~6-8.5 GHz, sold on modules, literally marketed for through-wall presence + respiration. lowest risk. our work becomes: antenna design, multi-module fusion for angle, DSP, viz. ~$100-250/module, need 2-3 for angle.
2. **build our own FMCW radar front end** in ~3-7 GHz. more novel, way more RF risk, board respins likely. probably the "we're a 2x team" flex but high blowup risk.
3. **WiFi CSI (ESP32)** through wall. cheapest, but noisy, usually wants a known transmitter on the far side, robustness is a grind. weaker demo. good fallback / backup slide.

lean approach 1 for the real project, keep 3 as the fallback story.

## what the actual deliverable looks like

- handheld or tripod box, screen shows top-down radar view, blips = moving people behind the wall in front of you.
- MVP: detect + range one moving person through one interior wall.
- v1: add angle (2-3 antennas), render position, track it moving.
- stretch: 2 people, breathing detection on a still person, wall-material auto-cal.
- dream slide only: silhouette.

## board / compute

- PCB carries radar front end + MCU. raw sample rates are high, so early prototyping probably streams IQ to a laptop for DSP, then we push the pipeline onto an MCU (STM32H7 class) or MCU+small FPGA.
- decide early: how much DSP has to be real-time on-board vs offloaded. this is the schedule risk.

## expo / grading angle

- novelty: "wallhack goggles" reads as novel even though motion-through-wall is old, because nobody's seen a cheap DIY one.
- crowd: live demo of blips moving as someone walks around behind a real wall is a magnet. bring a portable wall panel.
- risk to call out in proposal: RF is unforgiving, antenna + front end is the long pole, imaging is explicitly out of scope.

## open questions

- how many walls? one interior wall is realistic, exterior brick + multi-room is much harder.
- static clutter removal: need background subtraction, moving target indication. fine for moving people, can't see someone holding still without respiration trick.
- how do we get ground truth for testing? camera in the far room + timestamp sync.
- calibration knob for wall material / thickness is mandatory, propagation speed through the wall shifts range.
