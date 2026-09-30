
### Logical shift operators

The logical shift operators, << and >>, perform left and right shifts of their left operand by the number of bits given by the right operand. Both the << and >> shift operators fill the vacated bit positions with zeroes (0).

Examples:

```Verilog
integer start, result;
analog begin
	start = 1;
	result = (start << 2);
end
```

the integer result is assigned the binary value 0100, which is 0001 shifted to the left two positions and zero-filled.

```Verilog
integer start, result;
analog begin
	start = 3;
	result = (start >> 1);
end
```

In this example, the integer result is assigned the binary value 0001, which is 0011 shifted to the **right one position and zero-filled.**

[IEEE Std 1800-2017 (Revision of IEEE Std 1800-2012) IEEE Standard for SystemVerilog—Unified Hardware Design, Specification, and Verification Language](https://fpga.mit.edu/6205/_static/F23/documentation/1800-2017.pdf)

### Procedural and continuous assignments

SystemVerilog has two forms of the assign statement, as follows: 
- continuous assignments, placed **outside** any procedures
- procedural assignments, placed within a procedure

Continuous assignment statements are a separate process that are active throughout the simulation. The continuous assignment statement accurately represents combinational logic at an RTL level of modeling and is frequently used.

```Verilog
assign counter_gray = bin_to_gray(counter_bin);
// Whenever counter_bin changes, drive counter_gray
```

Procedural continuous assignment statements become active when the assign statement is executed in the procedure. The process can be deactivated using a deassign statement.

```Verilog
always @(posedge clk_src) begin
    counter_bin <= counter_bin + 1;
end
// changes only at the clock edge
```

[IEEE Std 1800-2017 (Revision of IEEE Std 1800-2012) IEEE Standard for SystemVerilog—Unified Hardware Design, Specification, and Verification Language](https://fpga.mit.edu/6205/_static/F23/documentation/1800-2017.pdf)
