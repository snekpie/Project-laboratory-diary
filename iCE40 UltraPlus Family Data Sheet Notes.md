
The iCE40 UltraPlus family of ultra-low power FPGAs has three devices with densities ranging from 2800 to 5280 Look-Up Tables (LUTs) fabricated in a 40 nm Low Pwer CMOS process.

These devices also feature:

- embdedded block RAM (EBR)
- Single Port RAM (SPRAM)
- on-chip oscillators (LFOSC, HFOSC)
- two hardened SPI controllers
- PWM IP
- three 24 mA RGB LED open-drain drivers
- I3C interface pins
- DSP blocks

### Architecture overview

The iCE40 UltraPlus family architecture contains an array of Programmable Logic Blocks (PLB), two oscillator generators, two user configurable I2C controllers, two user configurable SPI controllers, blocks of sysMEM embedded block RAM (EBR) and Single Port RAM (SPRAM) surrounded by Programmable I/O. 

![[iCE40UP5K top view.png]]

The PLB contains the building blocks for logic, arithmetic, and register functions. 

### PLB Blocks

The core of the iCE40 UltraPlus consists of Programmable Logic Blocks which can be programmed to perform logic and arithmetic functions. Each PLB consists of eight interconnected Logic Cells (LC), as shown in figure 3.2:

![[PLB Block Diagram.png]]

#### Logic Cells

Each Logic Cell includes three primary logic elements shown in figure 3.2:

- A four-input-Look-Up Table (LUT) builds any combinational logic function, of any complexity, requiring up to four inputs. Similarly, the LUT element behaves as a 16x1 Read-Only-Memory (ROM). Combine and cascade multiple LUTs to create wider logic functions.
- A D-style flipflop (DFF), with an optional clock-enable and reset control input, builds sequential logic functions. Each DFF also connects to a global reset signal that is automatically and immediately asserted following device configuration.
- Carry Logic boosts the logic efficiency and performance of arithmetic functions, including adders, subtracters, comparators, binary counters and some wide, cascaded logic functions.


##### Logic Cell Signal Descriptions


| Function | Type             | Signal Name    | Description                                                                                                           |
| -------- | ---------------- | -------------- | --------------------------------------------------------------------------------------------------------------------- |
| Input    | Data signal      | I0, I1, I2, I3 | Input to LUT                                                                                                          |
| Input    | Control signal   | Enable         | Clock enable shared by all LCs in the PLB                                                                             |
| Input    | Control signal   | Set/Reset      | Asynchronous or synchronous local set/reset shared by all LCs in the PLB.                                             |
| Input    | Control signal   | Clock          | Clock one of the eight Global Buffers, or from the general-purpose interconnects fabric shared by all LCs in the PLB. |
| Input    | Inter-PLB signal | FCIN           | Fast carry in                                                                                                         |
| Output   | Data signals     | O              | LUT or registered output                                                                                              |
| Output   | Inter-PFU signal | FCOUT          | Fast carry out                                                                                                        |

### CLock/Control Distribution Network

Each iCE40 UltraPlus device has six global inputs, two pins on the top bank and four pins on the bottom bank.They can be used as high fanout nets, clock, reset or enable signals. These dedicated global pins are identified as Gxx and each drives one of the eight global buffers.


### sysCLOCK Phased Locked Loops (PLLs)

The sysCLOCK PLLs provide the ability to synthesize clock frequencies. The iCE40 UltraPlus devices have one sysCLOCK PLL. REFERENCECLK is the reference frequency input to the PLL and its source can come from an external I/O pin.

EXTFEEDBACK is the feedback signal to the PLL which can come from internal routing or an external I/O pin. **The feedback divider is used to multiply the reference frequency and thus synthesize a higher frequency clock output.** 

The PLLOUT output has an output divider, thus allowing PLL to generate different frequencies for each output. The output divider can have a value from 1 to 64 (in increments of 2X). The PLLOUT outputs can all be used to drive the iCE40 UltraPlus global clock network directly or general purpose routing resources can be used.

Timing of the device registers can be optimized by programming a phase shift into the PLLOUT output clock which advances the delays the output clock with reference to the REFERENCECLK clock.

- it can be programmed during configuration
- it can be adjusted dynamically, here the PLL might lose lock after a phase adjustment on the output used as the feedback source and not relock until the tLOCK parameter has been satisfied.

![[PLL Diagram.png]]

### On-Chip Oscillator

The iCE40 UltraPlus devices feature two different frequency Oscillator:

1. LFOSC - tailored to run at low frequency. Runs at nominal frequency of 10 kHz. 
2. GFOSC - runs at nominal frequency of 48 MHz, divisible to 24 MHz, 12 MHz or 6 MHz by user option.