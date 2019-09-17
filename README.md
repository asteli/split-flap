# split-flap
A mechanical split-flap display in the style of the Solari boards, designed to be modular, manufacturable, and low-cost.

![A 10-module wide section of a split-flap display](https://github.com/asteli/split-flap/raw/master/character_module_nema17/process/10gang_dinrailmounted_july30.PNG)

What's happened so far:
I prototyped and built a 60-flap display around a NEMA 08 motor. I made two errors mainly: trying to package the motor inline with the hub that holds the flaps (this requires relatively complicated bracketry for the motor), and the motor ended up being undersized for the application. NEMA 08 motors don't have particularly much torque, and it was easily overwhelmed even for a low torque application like this.

The V2 is designed around a NEMA 17 motor, which paradoxically is much cheaper than the NEMA 08. Likely because it enjoys wide use in 3D printers and the like. They cost something like 5-8 USD wholesale. A friend happens to have a few hundred sitting around from an old project, which makes the choice even harder to resist.

The V2 also features a much larger character size (usable character area is roughly 60mm x 60mm -- old flaps were ~35mm wide), and is designed to be DIN rail mountable and powered/controlled via a common rail and bus for simplicity and reliability. The part count has come down significantly -- I've removed the (2) external bearings, as the NEMA 17 motor is large enough that I can trust its bearings to cope with the modest lateral loads involved.

When you are planning on building even 20 of something, every last part counts against you.
 
Update (September 16):
The CAD for the V2 split flap display is just about ready for a prototype build.
