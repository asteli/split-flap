# SplitFlapClock Design Notes

## November 28, 2019
Hastag HappyAlhamdulillahDay.

Status update:
	- Staying with NEMA 17.
	- Staying with a PCB per module
	- I'm going to use DIP switches to set addresses, because my previous scheme seems like a pain in the ass and the ease of confiuration with DIP switches is worth the extra cents. 

I'm starting the PCB design. Looks fairly simple. Going to be all DIGIKEY parts for this first build, I can try to go and cost optimize (chinese domestic components and assy) later. I wonder if I can find a vendor for the steel plates.

## September 16 2019

CAD for the newest version is mostly done. Have the mounting plate mostly designed up but I am going to simplify the geometry a bit to make it slightly easier to manufacture.

I am also looking at evaluating the 28byj-48 geared stepper motor in conjunction with a uln2003, apparently a popular configuration for small and low power actuation. 
	This motor appears to be quite small and have limited torque. Its max speed is something like 15RPM (0.25 Hz) which is more than fast enough for a split flap display. 

Vitaly is about to give me a shit ton of NEMA 17 motors. They are not D-flatted, which doesn't mean much in my application. I need to figure out how to size the bore of my hub to be a press fit that doesn't take massive amounts of force to attach/detach. Going slightly loose is an option as I can always use an adhesive (RTV? CA glue?) to retain it if the slip fit is a bit oversize.



## July 30 2019

Looking at using i2c for communications. Assuming 0.5A for 1 module, 7A continuous max amperage for 22AWG, that gives us 14 modules per power conductor pair. So doubling up (2x 24V, 2x PGND) gives us a decent amount of headroom. I want to use IDC connectors, just because fabricating the cable will be as simple as punching down a bunch of connectors on a shared ribbon, and it minimizes passthrough/daisy-chain failure modes. I was looking at a serial retransmit scheme like WS2812s or APA102s but I think this will be more reliable.

Question is how to assign i2c addresses to submissive devices. Scheme is as follows:

There _is_ a daisy chained "strobe" signal -- wait that doesn't work because we're using common bus IDC and ugh

OK how about maybe using prop delay? Naw sounds flakey.

Could do the classic i2c dip-switch or solder-blob jumper thing. Could do 6 bits, 64 permutations... should be good enough for anyone, right?

Tac switch on each device that when pushed, queries the bus for taken addresses, then assigns itself to the first open slot. Then saves its address to eeprom. Need an address reset mechanism, maybe by resetting uC with the "address set" switch depressed.

Then when the Dom controller powers up, it scans the bus for available submissives and assumes that they're physically in order from left to right.

Doing i2c means that a raspi or something can be in charge of setting all the things, which means I get to do the funky shit in python woooo. Plug a LORAN modem into the UART and bam, mesh connectivity.

Only needs to be set up once. We can also have modules ident using their built in single-character displays.

Issue in the model: my SOT23 hall-effect sensor sits about 5mm away from the outer surface of the flap disk. I would much rather it be something like 2mm. I guess I could just use a longer magnet... was spec'ing a 1/16" (Thick) x 1/8" (Diam) magnet before, could use a 1/8"x1/8" and that closes the gap by 0.3175/2 = 1.56 ish mm. goes down to 3.5mm. more reasonable. I think I can live with that. OK crisis averted. I am slightly worried about magnetizing my screws, in which case the hall effect sensor could pick up the screws as well as the magnet. Maybe use SS screws there. Or don't fucking magnetize them I guess.

Going to design a plate that links up with the DIN clip. It will set spacing and add a little bit of torsional stability to the assembled modules.


## July 29
I think I want to build the character displays in 4 or 5-gang modules. 20-gang is going to be super huge. 4-gang seems like a fun clock sized size. 5 would also work.
This would also be a good size for having one PCB control a number of them and not have insane looking harnesses running around. 

Local VMOT24 -> 5/3.3V for logic.

Connectivity:
UART
Ethernet
Power -- possible to do PoE? What's our PD? It's easy and cheap to do the typical powerpole terminals however.

TODO:
	- Retention tab

	- Model module mounting solution
		- DIN rail brackets?
		- DIN rail
	- Hall effect sensr
		- p/n
		- model
		- location
		- magnet location
	- PCB
		- location
		- functionality
		- connectivity



## June 3:

Other bearings came in. Pressed all the stuff together, forgot to align the flat on the motor shaft, broke the spokes out of the hub. 

That aside, there's still massive eccentricity in my otherwise round parts. I think I probably need to orient them such that they print with the rotational axis nearly straight up, despite the risk involved.

Other issues: Clearance between rotating parts. The further the material gets from the rotation axis or the fixing bearing, the more likely it is to rub. Keep clearances wide open if it doesn't affect functionality. No lost pride in having some air in the design.

The motor is indeed strong enough to rotate the display, but just barely. Eccentricity causes uneven lateral forces on the shaft that result in binding at one part of the rotation. Solution? Reprint hub.

Bearing seat were also rather tight. They did eventually fit but I think I want to add another 50 micron or more to the radius of the large seats. It's easy to get a bearing not to spin in a seat, lot easier than removing material to get it to press in.

Also, adding deep chamfers (1+ mm ) to the first edge of the bearing seat should help with getting them pressed in perpendicular in the first place. Use a press...

There was minimal issue putting the inserts in. May be a good idea to deepen the bores they go into. Chamfer couldn't hurt.

Oversizing the motor slot by 100um. Can glue it in just as easily. Little CA'll do ya.

I'm closing up the spoke-looking holes in the hub a bit to make it stronger. It warped there last time, and I fricken shattered it being an idiot.

Going 100um oversize on the bearing bore (up from 50um oversize). 

Added a little recess for a tiny chunk of retroreflector to sit in.

Todo on the enclosure:
Find a way of affixing the leaf that doesn't look like jank
Remove the slot and tab stuff from the front.
Enlarge the hole for the motor connector access.
Get rid of the leaf spring entirely and just use the acrylic to detain the flaps.

Todo on the motor mount:
Bore deeper for the inserts
Chamfer the outside edge at the far end of the motor

Todo on Flaps:
Generate Template for cutting

Mid July:
Ditched the old design. The motor was a bit small for what it was doing, and worse, NEMA 08 motors are expensive. I'm moving to a NEMA 17 motor, which can be had for $5 a piece perhaps (and Vitaly has a lot, 400 something of these motors just sitting around).

Redesigned, removing the near and far end bearings -- the bearings in the motor itself are now big enough to handle the radial loads. This brings down part count significantly.
Also I'm going to ditch the per-module enclosure, and modify the design such that the motors nest inside the adjacent hubs. Per-module structure goes from:

Acrylic enclosure
Motor mount block
Motor mount bearing
Motor NEMA 08
Hub
Hub disks
Flaps
Far end bearing
Far end bearing block


To:
Sheet metal bracket
Motor NEMA 17
Hub
Hub disks
Flaps

Also I'm downsizing the flaps disks from 1/8" acrylic down to 1/16" because it's cheaper and the loads involved are very small. This will allow me to squeeze PHSCHS or SHCS screws in to fasten the disks, meaning that I don't have to counter sink those self-tapping M2.5 clearance holes.



## May 29

Loaded the models into preform. I tweaked the printed parts to use brass M3 threaded inserts instead of screwing directly into the plastic. Had to increase the size of a couple parts in order to make that work.

I'm going to screw directly to the hub to attach the acrlic flap retainer disks.

Laying out the components in Preform, I'm making sure to orient the parts to keep most if not all of the support structure touchpoints out of the bearing areas so that we don't get interference when I try to press the bearings in.


## May28

Added the encoder. Shortened the flaps just slightly, looks like it can't jam now. Not sure about what material to use for the flaps still. Acrylic likely too brittle. Thinking delrin or PET?

Ordered all materials. Came out to $200. Haven't worked out the spring thing bit yet. Considering harvesting the steel clip off of a pen to make the first article.

## May 27 2019

Aspect ratio of the flaps is interesting. Making them long makes the space needed to do the angle reversal after the card is displayed bigger, and thus drives part of package size.

I'm noticing that certain design choices are ... convergent. Like having the tabs on the sides of the flaps, or having a certain aspect ratio in order to get the packaging to make sense. (taller flaps mean the whole enclosure has to get taller to give them space at the bottom of the loop)

I'm envisioning an even less expensive design for light duty use that uses DC gearmotors and a grey code optical encoder printed on the side of the hub. uC on the back has just one FET to turn the motor on, and reflectivity pickups. If sensors don't see the right code for the commanded character, spin the motor.

The bearings are massive overkill as well. For a clock sort of function a set of low friction plastic or brass bushings would work great. Very little load. I'm going to use ball bearings because they're what I know. Also they look pretty slick.

I'm not quite sure yet how I want to do the control. One way would be to have a separate controller on each module and daisy chain them a la serially addressable LED chains. You'd need a host somewhere that could spit UART at the modules. Do we want checksums here? Is that overkill? There are super low-tech ways to go about the control. If one was willing to index using an encoder of some kind, it's certainly possible to use basic logic gates and a DC gearmotor to servo to a position encoded as binary or BCD over 6 logic bits. Very 80s. Maybe next rev.

I'm going to need to cut the lower lip of the front opening on the enclosure down low enough that the flaps can't jam up against it. sort of a liability as it stands now, the rotor could push the flaps down into it and sieze the mechanism. Maybe shorten the flaps ... again ... if the enclosure cut looks untenable.

I just realized I can install the encoder or reflectometer or whatever -- the positioning thingy into the far side bearing block. Got loads of space there and I can make a cut in the side of the casing just like for the motor wires. Don't think there's enough space to put an actual full encoder on there right now, although I could distribute the sensors throughout the disk around the bearing. But that's an unwieldy solution, to say the least.

Rotate the bearing block holes 180 and put two screw holes in the block to attach a PCB, add corresponding void in the LH enclosure plate, run wires to the PCB out back. Echo Zulu. Reflecto-dot can go on the hub itself, or optionally the disk. Each one could probably benefit from calibration at time of programming.

### Questions:

The stepper motor I'll probably be using is a NEMA 8 (0.8" square section), with a 1.8 degree full step size. That doesn't really line up nicely with the 6 degree increments between symbol changes. I can use microstepping to even out the rotation. I might have to leave the motors on all the time to keep the rotor at an unnatural resting spot between steps. Also possible that backlash in the mechanism will absorb the last fraction of a rotation after the stepper is powered off. If it ends up being an issue I may have to bite the proverbial bullet and go up in motor size up to a NEMA11 to get a stepper with an 0.9 degree step size. While not exactly a clean divisor, that gives me nearly 7 steps per symbol change. It would also make the entire mechanism a lot bigger. Boo.

How to size tab-and-slot construction for the acrylic enclosure? Can I use nominal dimensions and hope that the laser cutter's kerf takes care of the clearance for me?

What about sizing bores for bearings? I don't know how far off-nominal the housings are going to be. Does it make sense to oversize the bores so that there's maybe a bit of slop, then to fill it in with adhesive? Or to leave it nominal dimension and try and press-fit them? Will that crack the printed plastic parts?

How much bending compliance can I expect from the flaps? Is the retention mechanism that we're using to do the "detent" more reliant on the flap bending or the retention tab bending? Can I use the enclosure itself to do the flap detention?
