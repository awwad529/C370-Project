# Hardware -- train-logger (Awwad Alsubhi, Kian Jiang)

## Order status
- **Sensors:** ordered 2026-09-23 from Amazon, shipping to Kian, estimated
  delivery 2026-09-24. Screenshot: `hardware-order.png`.
- **Raspberry Pi:** not yet ordered. Plan: Raspberry Pi 4 starter kit
  (~$140), to be ordered 2026-09-25.
- **microSD card:** not yet ordered. 32GB, A1/A2 (SanDisk or Samsung), to
  be ordered 2026-09-25 with the Pi kit.
- **Resistor kit:** not yet ordered. Must include 1kΩ and 2kΩ, to be
  ordered 2026-09-25 with the Pi kit.

## Parts
| Part | Qty | Role in the design |
|------|-----|--------------------|
| INMP441 I2S MEMS microphone | 3 | **Sensor A:** horn detection, the early cue. Spares for fault injection. |
| HC-SR04 ultrasonic sensor | 5 | **Sensor B:** confirms the train is there and times arrival and clear. |
| Raspberry Pi 4 starter kit | 1 | Host board. **Pending**, see above. |
| 32GB microSD card (A1/A2, SanDisk or Samsung) | 1 | OS and event-log storage; not included in the Pi kit. **Pending**, see above. |
| Resistor kit (incl. 1kΩ and 2kΩ) | 1 | HC-SR04 echo voltage divider. **Pending**, see above. |

## Wiring notes
- **Always wire with the Pi powered off** (spec Section 17, Appendix A).
- **HC-SR04 echo is 5V; Pi GPIO is 3.3V with no 5V-tolerant inputs.** The
  echo line needs a voltage divider (spec Section 17, Appendix A). We use
  1k (series) / 2k (to ground): 5V x 2/(1+2) = 3.3V at the GPIO. Trigger
  can be driven directly from 3.3V GPIO. Check the divider output with a
  meter on a breadboard before connecting the Pi.
- **INMP441 is 3.3V over I2S.** Power it from the Pi's 3.3V rail, not 5V.

## Known limits
- **HC-SR04 range is roughly 4 m.** The sensor must be mounted close to
  the tracks at Laurel/Mason, which makes the mounting-spot risk in
  `PROBLEM.md` harder.
- **Neither sensor is weatherproof.** Both need an outdoor enclosure for
  the 48-hour deployment. The HC-SR04 transducers and the mic port still
  need an open path to the outside.
