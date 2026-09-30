28.9.2026.

### Clock Domain Crossing

Clock Domain Crossing is the traversal of a signal in a synchronous digital circuit from one clock domain to another. If a signal does not assert long enough and is not registered, it may appear asynchronous on the incoming clock boundary.

A synchronous system is composed of a single electronic oscillator that generates a clock signal and its clock domain – the memory elements directly clocked by that signal from that oscillator and the combinational logic attached to the outputs of those memory elements.

In a synchronous domain, size of the domain is inversely proportional to the frequency.

Different clock domains have a different frequency, phase or both. Relationship between the clock edges cannot be relied upon.

Reference:
[Clock domain crossing - Wikipedia](https://en.wikipedia.org/wiki/Clock_domain_crossing)

Clock Domain crossing occurs whenever a signal generated in one clock domain is used in another clock domain. It is one of the most challenging aspects of digital design and a frequent source of subtle, hard-to-debug failures.

The fundamental problem is that when a signal changes near the active edge of a clock in the receiving domain, the receiving flip-flop may enter an unstable state called metastability.

What is metastability?

A flipflop has strict timing requirements: the input data must be stable for a setup time before the clock edge and hold time after the clock edge. When this is violated, the flip-flop may enter metastability – a state where the output voltage hovers between logic 0 and logic 1, unable to settle to a valid logic level.

```
Valid Operation:
Data:  ___/‾‾‾‾‾‾‾‾‾‾‾
       Setup | Hold
Clk:   _____|‾‾‾|_____
                ↑
            Clean capture

Metastable Condition:
Data:  _____/‾‾‾‾‾‾‾‾
Clk:   _____|‾‾‾|_____
            ↑
       Data changes during
       setup/hold window

```

The probability of metastability persisting for time *t* decreases exponentially:

```
P(metastability) = (T₀ / Tclk) × e^(-t/τ)

Where:
- T₀ = window of vulnerability (setup + hold time, ~10-100ps)
- Tclk = clock period
- t = available resolution time
- τ = metastability time constant (~10-100ps, process-dependent)
```

We cannot eliminate metastability, but we can reduce its probability to acceptably low levels by producing sufficient resolution time.

### Two-Flip-Flop solution

```Verilog
module two_ff_syncronizer (
	input wire clk_dest,
	input wire rst_n,
	input wire async_data, // asynchronous input
	output wire sync_data  // synchronized output
	);
	
	reg sync_ff1, sync_ff2;
	
	always @(posedge clk_dest or negedge rst_n) begin
		if (!rst_n) begin
			sync_ff1 <= 1'b0;
			sync_ff2 <= 1'b0;
		end else begin
			sync_ff1 <= async_data; // may go metastable
			sync_ff2 <= sync_ff1;   // very unlikely to be metastable 
		end
	end
	
	assign sync_data = sync_ff2;
	
endmodule
```

- this works because the chance of the first flipflop entering metastability is possible but for the second one it is negligible

*I remember seeing this implementation in the LPI-Q and LPI-P protocols. The LPI-P protocol is used for controlling the power level of a subordinate. The recommended implementation included two-FF solution on receiving the data.*
![[LPI-P implementation.png]]

Figure 3-2 Recommended P-Channel clock domain crossing synchronization1
### Critical Design Rules for Designers

#### Rule 1: Never use synchronizer outputs in same cycle

```Verilog
// WRONG - Using sync output immediately
always @(posdegle clk) begin
	sync_ff1 <= async_in;
	result <= sync_ff1 & other_signal; // Timing violation!
end

// CORRECT - give full cycle for resolution
always @(posedge clk) begin
	sync_ff1 <= async_in;
	sync_ff2 <= sync_ff1;
	result <= sync_ff2 & other_signal; // Safe
end
```

#### Rule 2: Protect synchronizer Flip-Flops from optimization

Synthesis tools might optimize away or duplicate synchronizer flip-flops, breaking the synchronizer. Use synthesis attributes:

```Verilog
(* ASYNC_REG = "TRUE" *) reg sync_ff1;
(* ASYNC_REG = "TRUE" *) reg sync_ff2;

// Or synthesis directives
// synthesis syn_keep = 1
// synthesis preserve
```

#### Rule 3: one bit at a time

Never synchronize multi-bit buses directly with simple synchronizers - bits may arrive at different times due to different metastability resolution, causing glitches:

```Verilog
// WRONG - Multi-bit synchronizer
always @(posedge clk_dest) begin
	sync_ff1 <= multi_bit_bus[7:0]; // BAD!
	sync_ff2 <= sync_ff1;
end
```

### Three-FF synchronizers
For extremely high-reliability applications or very fast clocks, use three flip-flops:
```Verilog
always @(posedge clk_dest or negedge rst_n) begin
	if (!rst_n) begin
		sync_ff1 <= 1'b0;
		sync_ff2 <= 1'b0;
		sync_ff3 <= 1'b0;
	end else begin
		sync_ff1 <= async_data;
		sync_ff2 <= sync_ff1;
		sync_ff3 <= sync_ff2;
	end
end
```

This reduces metastability probability to effectively zero (<< 10^-30 failures per second) at the cost of additional latency.

### Gray Code for counters

When crossing m
![[Gray code circuit diagram.png]]

example:

```Verilog
// Binary to Gray code converter
function [3:0] bin_to_gray;
	input [3:0] bin;
	begin
		bin_to_gray = bin ^ (bin >> 1);
	end
endfunction

// Source domain
reg [3:0] counter_bin;
wire [3:0] counter_gray;

always @(posedge clk_src) begin
	counter_bin <= counter_bin + 1;
end

assign counter_gray = bin_to_gray(counter_bin);

// Destination domain - safe to synchronize
reg [3:0] counter_gray_sync1, counter_gray_sync2;
always @(posedge clk_dest) begin
    counter_gray_sync1 <= counter_gray;
    counter_gray_sync2 <= counter_gray_sync1;
end

// Gray to binary converter for use
function [3:0] gray_to_bin;
    input [3:0] gray;
    integer i;
    begin
        gray_to_bin[3] = gray[3];
        for (i = 2; i >= 0; i = i - 1)
            gray_to_bin[i] = gray_to_bin[i+1] ^ gray[i];
    end
endfunction

wire [3:0] counter_bin_dest = gray_to_bin(counter_gray_sync2);
```

[Clock Domain Crossing](https://chipverify.com/rtl-synthesis/clock-domain-crossing)

System on a chip (SoC) is an integrated circuit that combines most or all key components of a computer or electronic system onto a single microchip. It usually contains memory, CPU, input/output, Wi-Fi connectivity, radio frequency processing.

[System on a chip - Wikipedia](https://en.wikipedia.org/wiki/System_on_a_chip)

### Possible issues due to CDC

1. Metastability (MTSB)
2. Data loss (DL). Data loss issue, as the name suggests vauses some transitions or data to be lost during the transfer from one clock domain to the other. This primarily happens when the source domain does not hold the value of the pulse being synchronized for long enough.
3. Data Incoherence (DI). 

![[CDC case tree.png]]

Read more here: [Clock domain crossing (CDC) - The complete reference guide - thedatabus.in](https://thedatabus.in/cdc_complete_guide/)

