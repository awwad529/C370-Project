# Problem memo -- train-logger (Awwad Alsubhi, Kian Jiang)

## The user
**Awwad Alsubhi**, an off-campus CSU student who crosses the rail line at
**W Laurel St & S Mason St** every day, and **Fred** of the Fort Collins
International Center, who suggested the idea. More broadly, we represent
off-campus students whose commute to campus runs through that crossing
on foot, bike, or by car.

## The problem
When a train blocks Laurel/Mason, a student approaching it cannot tell
whether the crossing is blocked or how long it will stay that way until
they are already committed to the route. Awwad has been stopped there
repeatedly and arrived late to class. His estimate is 10-15 min per
blockage, **unmeasured** (from memory, not timed). That is in line with
local reporting: John Howe told CBS Colorado (2020) that Fort Collins
crossings "seem to vary from anywhere between 10-20 minutes." Our logged
data will replace both figures.

## Why a device
A phone app fails the test. A phone cannot sense a train, it is only at
the crossing when its owner is, and crowd-report apps depend on someone
tapping a button, so their record has gaps, especially at 3 a.m. A
local precedent shows this: a Fort Collins resident's camera-based "Train
Alert" system detected trains on the Mason St corridor with cameras at the
tracks. It is now offline "due to challenges associated with hosting
cameras along the tracks." The only local solution that worked was a
physical sensor at the tracks, and nothing has replaced it. There is no
public live data for this crossing. Our device at the crossing **writes**
a complete, timestamped log. The phone is only where people **read** it.

**Scope:** a live "crossing blocked now" status plus a history of when and
for how long blockages happen. We do not predict train arrivals far ahead.

## The sensors
An I2S microphone (e.g. INMP441) gives the **early cue**: the horn sounds
before the train is close. An HC-SR04 ultrasonic sensor **confirms
presence** and times arrival and clear. Fused, they log pass time, pass
duration, and road-blocked time, and they check each other: horn without
a train is logged as a **false alarm**, and a train without a horn is
logged as a **silent train**. If one sensor is unplugged, the system
degrades to **single-sensor mode** and keeps logging, with those entries
marked as single-sensor.

## The mechanisms
- **(F) No-drop audio pipeline:** a single dropped buffer can lose a horn,
  so capture-to-detection must never silently drop samples.
- **(D) Crash-consistent append-only log:** the log *is* the product, and
  outdoor power is unreliable. A power cut must never corrupt or lose
  committed entries.

## The risk
Train Alert died because of hosting hardware along the tracks. Our main
risk is the same: securing a **legal, powered, weather-protected mounting
spot** with a view of Laurel/Mason for 48+ hours. *Secondary:* if the
crossing is a horn quiet zone, horns may be unreliable. The fallback is
ultrasonic-primary, with the mic used as corroboration.

---
*Sources:* jimhaselmaier.com/trainalert.html (Train Alert, offline notice);
CBS Colorado (2020), John Howe quote on Fort Collins crossing times,
https://www.cbsnews.com/colorado/news/fort-collins-train-alert-john-howe/
