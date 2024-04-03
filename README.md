# DIY-Linear-Motor
https://www.youtube.com/watch?v=wEBWoIjgftI

This is an attempt to make a high performance and low cost linear motor for the diy hobby user. This can be adapted to any use case as needed, 3D printer, pick and place etc. See here for background on operating principle.

https://en.wikipedia.org/wiki/Linear_motor

## Research and Sim (coil and magnet) + Market Options

First steps was research exisiting designs and perform simulations where possible. 

The following two projects were very helpful in the early stages:
- https://www.instructables.com/DIY-IRONLESS-LINEAR-SERVO-MOTOR/
- https://github.com/TkkrLab/LinearMotor

Summary of coil and magnet pitch is also as follows.
Since there is conflicting arrangements of coil and magnet pitches based on the information available some simulation was required. Using Ansys electrical some key parameters were discovered, both flat and round motor designs were tested but only the round was pursued further due to the easier construction techniques. Starting with permanent magnets and the magnet tube, there is commercially available 13mm OD - 12.1mm ID aluminium tube https://tinyurl.com/5435frky that is available for hobby plane builds, this can be purchased locally in 1m lengths and bears the label xxx from USA. Therefore 12mm OD permanent magnets fit snugly inside with little wasted space. The available lengths of 12mm permanent magnets vary from 15-30mm, this sets the both the magnet pitch and the coil pitch which affects the overall length of the forcer and available stroke. I decided to go with 20mm long magnets, which makes the N-N magnet pitch at 40mm. Inputing this into the simulation, the coil design was refined until a clean three phase back EMF was obtained, this was an iterative process that ended up with a coil spacing of 6.8mm. Once the spacing of the coils and magnets were fixed, optimisation of the number of turns and wire gauge was next. A target for maximum force to amperage ratio was slightly preferred over force to weight ratio. Using some initial parameters set by the motor driver (more details below), the phase to phase resistance should be greater than 10 ohms, with a design voltage of 35V means peak current would be approximately 3.5 amps, with the motor driver capable of 5 amp peak. Starting off with one set of three coils, each coil should provide 5 ohms of resistance, using the website (coil24) ID is fixed at 14mm to allow for clearance to the 13mm magnet tube, coil width is set to 6.35mm so wiring and the 6.8mm spacing can be honoured (more details later). Different wire gauges were tested keeping the above parameters the same and changing the number of turns to suit. This was repeated for dual and triple coils in series but with invertred winding direction. The summary of the findings are below.
![image](https://github.com/cmore839/DIY-Linear-Motor/assets/85714349/eebe09a1-eace-40f4-a375-98fe96b4f446)

The dual coil set was picked since the efficiency of force/amperage was quite high without going to the extra effort of make 9 coils for a triple set.

## Electronics Market

Alot of this project was enabled by the keen interest in the Arduino-FOC project being run over at https://docs.simplefoc.com/. There is a huge amount of information and support to get normal motors running in closed loop with great performance. I was keen to try and get a linear motor running using the same platform.

After some learnings and process of elimination the best combination of electronics was followings
- Ardunio brain: STM32 NUCLEO-G474RE
- Motor Driver SimpleFOCShield v2.0.4 (Aliexpress): https://docs.simplefoc.com/arduino_simplefoc_shield_showcase
- Encoder (Aliexpress): HEDS9740 360LPI

The NUCLEO-G474RE is the workhorse of the STM32 family with the highest clock speed and has been fully backwards compatible with SFOC library up to date.

Motor driver is based on the L6234 from ST, this is a well known chip which has a really nice implementation on the SFOC Shield with built in current sensing. In addition it has voltage regulator and an optimised layout which made me not reinvent the wheel on this aspect.

In order for closed loop control to be sucessful a linear encoder is needed the HEDS9740 360LPI was chosen due to the cost and performance ratio. In quadrature mode, a single step should be 17 micrometers, for bigger budget projects the RLS magnet strip and board level incremental encoder would be great, this could give 5 micron step size.

## Mechanical build (electronics below)

Magent tube - this was relatively straightforward, I decided to super glue two individual magents together to reduce the overall length that needed to be compressed, this was a small jig in a vice made out of PE so the glue wouldn't stick, it was debatable if this was worth it. Next was to insert all of the magnets into the aluminium tube with one end capped with tape and a wooden dowel inserted first to move the first magnet away from the end of the tube. With a liberal amount of 2 part epoxy placed in the tube and on each magnet, the magnets are all placed in the same pole facing one another, this means a clamping force is required overnight while the glue sets, a simple woodworking clamp did the trick.

### Coils - Mechanical

Since this is a round linear motor, the winding process was slightly easier than a rectangular flat coil for a u-shaped linear motor. It took quite a lot of trial and error even to get a average looking coil, this all required testing different glues and jigs. I ended up using my 3 axis CNC router with a servo as a spindle, this essentially gave me a very large coil winding machine. The coil arbors we're made out of HDPE with a shoulder screw being the central core giving strength and straightness. The wire needs a guide close to the arbor as well as a way of adding tension to the system. In the end I used an old inflatable ball needle for the wire guide and a cloth in a vice provided the winding tension. https://youtu.be/FTWShWmTZQY The coil I ended up making was the following:
![Coil](https://github.com/cmore839/DIY-Linear-Motor/assets/85714349/5ed4f1ec-23f9-4d8c-a79b-02f437204583)

As I was using the CNC router I just wrote a gcode to do the coil winding number of turns while moving a single axis along the pitch of the coils and tweaked it from there if the coil jumped or didn't fill properly. This turned out to be very difficult, especially since it has 16 layers and 249 turns, getting everything sitting perfectly was fairly unachievable. I eventually got a code that worked okay as well as working on the basics need for the arbor design and the glue needed, since the coil needs constant tension or else it will unwind, I ended up using a two part epoxy that was applied between every layer while winding this had to sit overnight, and once set, placing the arbor in the freezer for 5 mins freed the copper coil easily. Other glues that I tried and failed were superglue, nail varnish and timber varnish. Once you have your six coils done with about 10 cm of flying lead on each end I assembled them together and then wired up in sequence. See schematic below for an overview of mechanical layout and electrical wiring.

![Linear Motor 23-07-23](https://github.com/cmore839/DIY-Linear-Motor/assets/85714349/3df903dd-f8b5-42af-b077-5285db77528d)

In order to get the coils spaced to the simulation value, I used small hole punch circles of plastic to sit between each layer, these were superglued in place as a temporary holder to achieve the 6.8mm coil pitch, this needs to be done for each coil and then placed in the correct configuration (see layout drawing of coil polarity). For my prototype I aligned the six coils of the internal diameters with another HDPE Arbor, this ensured the clearance around the magnet tube is kept even without scratching. I used some more two part epoxy to set this all in place. Soldering the ends of each coil in series and delta Y configuration is again as per the coil layout drawing, leaving just the three phases A,B,C for connection to the motor driver.

## Assembly

The coil set sits on a small mgn9 linear bearing carriage and rail, I machined some small brackets from acrylic sheet, this held the magnet tube in place and secured the coils over the whole length of travel. There is plenty of room for improvement in the brackets, but for a prototype this wasn't the priority. The linear encoder head was attached to the moving carriage and the encoder strip was attached to the magnet tube end brackets, one end was a M3 countersunk screw and the other end was a wire flexure that kept the strip tight. Cable strain relief was a problem which needed to be paid attention to, mostly due to the sensitive pin terminations on the encoder; this was fixed later with an encoder breakout board (see picture).

## Motor driver - setup and first run (SimpleFOC)

A Lot of this project was centered around the possibility of getting the SimpleFOC Arduino library working with a linear motor, given the limited information this was tricky to navigate initially. Hopefully this helps with other people trying to get up and running.

### First steps - 
Once you've got the SFOC library installed as well as the STM32 offical library from STMicro (very important), you are ready to do some pre-checks and calibration.
# Encoder Check
Under the SFOC example list, utilities, sensor test, and select your appropiate encoder, in this case just standed AB encoder. This checks the pinout for your encoder. Once you get a 

