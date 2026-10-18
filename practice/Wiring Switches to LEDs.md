### Project 1: wiring switches to LEDs

As my first encounter with the iceBreaker v1.0e development board, I decided to follow a book by Russel Merrick, "Getting Started with FPGAs". This book contains practical examples for ice40 family FPGAs and it uses them as an example a lot of the time.

In chapter 2, the author gave a first task. Wire 4 switches to 4 LEDs, so while a switch (button in our case) is pressed, then the LED wired to it lights up. The original Verilog code from the book is as follows:

```Verilog
module Switches_To_LEDs (
	input i_Switch_1,
	input i_Switch_2,
	input i_Switch_3,
	input i_Switch_4,
	
	output o_LED_1,
	output o_LED_2,
	output o_LED_3,
	output o_LED_4
);

assign o_LED_1 = i_Switch_1;
assign o_LED_2 = i_Switch_2;
assign o_LED_3 = i_Switch_3;
assign o_LED_4 = i_Switch_4;
```

I decided to write Verilog code just for 3 pins and switches since 3 buttons and 5 LEDs are connected to single PMOD pins.

I decided to download Yosys, an open-source software which is used for hardware synthesis, as it doesn't require a license and is open-source. It supports the iCE40 FPGA family and is a part of a well-established FPGA development toolchain.

Another reason for choosing Yosys is that its integration with other open-source tools, such as nextpnr for place-and-route and project IceStorm for generating the FPGA configuration bitstream and programming the device. These tools provide a complete development flow altogether.

What I did was the following:

#### Writing Verilog code

As I mentioned above, I used only 3 LEDs for testing the code. My code was the following:

```Verilog
\\ switches_to_leds.v
module Switches_To_LEDs (
	input i_Switch_1,
	input i_Switch_2,
	input i_Switch_3,
	
	output o_LED_1,
	output o_LED_2,
	output o_LED_3
);

assign o_LED_1 = i_Switch_1;
assign o_LED_2 = i_Switch_2;
assign o_LED_3 = i_Switch_3;
```

I opened Windows PowerShell for this from my folder and switched to the directory where my code is:

```powershell
cd C:\fpga\projects\icebreaker-led
```

#### Synthesis

I ran the following command:

```powershell
yosys -p "read_verilog switches_to_leds.v; hierarchy -top Switches_To_LEDs; synth_ice40 -top Switches_To_LEDs -json switches_to_leds.json"
```

#### Place and Route

I first created a pins.pcf pins constraint file after I traced the pins of the LEDs and the buttons by reading the connections on the back of the dev board and checking the schematic for connections between single mode PMOD and FPGA pins. this was my pcf file:

```verilog
# Pin constraint file for LED blink

set_io o_LED_1 26
set_io o_LED_2 27
set_io o_LED_3 25

set_io i_Switch_1 20
set_io i_Switch_2 19
set_io i_Switch_3 18
```

After that, I ran this command:

```powershell
nextpnr-ice40 --up5k --package sg48 --json switches_to_leds.json --pcf pins.pcf --asc switches_to_leds.asc
```

to generate the .asc file.

#### Generating the bitstream

The last step was generating the bitstream, which I did using this command:

```powershell
icepack switches_to_leds.asc switches_to_leds.bin
```

#### Programming the FPGA

the last step was this command:

```powershell
iceprog switches_to_leds.bin
```

and finally, the setup worked. 

I was exploring the commands using ChatGPT and it suggested to create a PowerShell script which would automate all of the commands I wrote above and it suggested something like this:


```powershell
\\ build.ps1
$ErrorActionPreference = "Stop"

Write-Host "Stage 1: Synthesis" -ForegroundColor Cyan
yosys -p "read_verilog switches_to_leds.v; hierarchy -top Switches_To_LEDs; synth_ice40 -top Switches_To_LEDs -json switches_to_leds.json"

if ($LASTEXITCODE -ne 0) {
    throw "Synthesis failed."
}

Write-Host "Stage 2: Place and route" -ForegroundColor Cyan
nextpnr-ice40 --up5k --package sg48 --json switches_to_leds.json --pcf pins.pcf --asc switches_to_leds.asc

if ($LASTEXITCODE -ne 0) {
    throw "Place and route failed."
}

Write-Host "Stage 3: Bitstream generation" -ForegroundColor Cyan
icepack switches_to_leds.asc switches_to_leds.bin

if ($LASTEXITCODE -ne 0) {
    throw "Bitstream generation failed."
}

Write-Host "Build successful: switches_to_leds.bin" -ForegroundColor Green
```

### Important notes

When I connected the dev board to my laptop using micro-USB cable, my laptop could not execute the following commands since it did not recognize the device. It showed an error with vendor_id and device_id. I downloaded Zadig and replaced the driver for the Dual RS232-HS interface, after which I could complete the execution of the commands.

### Project 2: Lighting an LED with Logic Gates

This is a second project from the "Getting Started With FPGAs book" by Russel Merrick, in chapter 3.

This project should illuminate an LED, but only when two switches are pushed at the same time. In other words, first LUT is used by implementing an AND gate. I tried to implement it without looking at the solution. Since we have 2 switches and 1 LED as the task, I would write the Verilog code the following way:

```Verilog
module AND_Gate (
	input i_Switch_1,
	input i_Switch_2,
	
	output o_LED_1
)

assign o_LED_1 = i_Switch_1 & i_Switch_2;

endmodule \\ AND_Gate
```


An interesting part of the report after running iceprog is this:

```powershell
Info: constrained 'o_LED_1' to bel 'X18/Y31/io0'
Warning: Ignoring trailing PCF settings (on line 5)
Info: constrained 'i_Switch_1' to bel 'X21/Y0/io1'
Warning: Ignoring trailing PCF settings (on line 6)
Info: constrained 'i_Switch_2' to bel 'X22/Y0/io1'

Info: Packing constants..
Info: Packing IOs..
Info: Packing LUT-FFs..
Info:        1 LCs used as LUT4 only
Info:        0 LCs used as LUT4 and DFF
Info: Packing non-LUT FFs..
Info:        0 LCs used as DFF only
Info: Packing carries..
Info:        0 LCs used as CARRY only
Info: Packing indirect carry+LUT pairs...
Info:        0 LUTs merged into carry LCs
Info: Packing RAMs..
Info: Placing PLLs..
Info: Packing special functions..
Info: Packing PLLs..
Info: Promoting globals..
Info: Constraining chains...
Info:        0 LCs used to legalise carry chains.
Info: Checksum: 0x55b93e15

Info: Device utilisation:
Info:            ICESTORM_LC:       3/   5280     0%
Info:           ICESTORM_RAM:       0/     30     0%
Info:                  SB_IO:       3/     39     7%
Info:                  SB_GB:       0/      8     0%
Info:           ICESTORM_PLL:       0/      1     0%
Info:            SB_WARMBOOT:       0/      1     0%
Info:           ICESTORM_DSP:       0/      8     0%
Info:         ICESTORM_HFOSC:       0/      1     0%
Info:         ICESTORM_LFOSC:       0/      1     0%
Info:                 SB_I2C:       0/      2     0%
Info:                 SB_SPI:       0/      2     0%
Info:                 IO_I3C:       0/      2     0%
Info:            SB_LEDDA_IP:       0/      1     0%
Info:            SB_RGBA_DRV:       0/      1     0%
Info:         ICESTORM_SPRAM:       0/      4     0%
Info:         SB_FILTER_50NS:       0/      2     0%
```

which tells about the device utilization. It definitely seems like an overkill to create a single AND gate from an FPGA :)

3 I/O ports are used and one LUT.