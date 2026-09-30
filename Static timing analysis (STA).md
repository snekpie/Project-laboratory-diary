STA is a method of validating the timing performance of a design by checking all possible paths for timing violations. STA breaks a design down into timing paths, calculates the signal propagation delay along each path and checks for violations of timing constraints inside the design and the input/output interface.

### How does STA work?

STA first breaks down the design into timing paths. Each timing path consists of the following elements:

- **startpoint**: the start of a timing path where data is launched by a clock edge or where the data must be available at a specific time. Every startpoint must be either an input port or a register clock pin.
- **combinational logic network**: elements that have no memory or internal state. Combinational logic can contain AND, OR, XOR, and inverter elements, but cannot contain flip-flops, latches, registers or RAM
- **Endpoint**: the end of a timing path where data is captured by a clock edge or where the data must be available at a specific time. Every endpoint must be either a register data input pin or an output port.

A combinational logic might contain multiple paths. STA uses the longest path to calculate a maximum delay and the shortest path to calculate a minimum delay.

![[STA paths.png]]

STA also considers the following types of paths for timing analysis:

- **Clock path**: a path from a clock input port or cell pin, through one or more buffers or inverters, to the clock pin of a sequential element, for data setup and hold checks.
- **Clock-gating path**: a path from an input port to a clock-gating element, for clock-gating setup and hold checks
- **Asynchronous path**: a path from an input port to an asynchronous set or clear pin of a sequential element, for recovery and removal checks.

![[STA types of paths.png]]
[What is Static Timing Analysis (STA)? – How STA works? | Synopsys](https://www.synopsys.com/glossary/what-is-static-timing-analysis.html)

### Timing arcs

Timing arcs are parameters defined in the process library for each cell which define the delays of the cell across different PVT corners. Static timing analysis uses those parameters to calculate the max and min delay through any library cell. For example, for a D flipflop, technology library will include the parameters that define the setup time, hold time and C (clock) to Q (output) time (TCQ). 

TCQ is defined as the time it takes for data to appear on output Q once clock C is triggered (posedge or negedge).

**Setup time:** the time the input D must be stable before the clock C is triggered is defined as setup time. If data is not stable at least setup time before the clock edge, output will be undetermined.

**Hold time**: the time the input D must be stable after the clock C is triggered. If the data is not stable for at least hold time after the clock edge, output will be undetermined.

STA can be done on both sequential and combinatorial parts of the design. In any sequential design path there is always one launch flop (driving the data) and one latch flop (capturing the data). Combinational paths can also be considered as sequential paths by assuming a virtual clock driving a virtual launch or virtual latch top.

#### Constraints

**Setup constraint:** the setup constraint of any digital circuit is defined as the timing constraint so that the slowest path in the design must meet setup time of the latch flip flop.
**Hold constraint:** the hold time of any digital circuit is defined as the timing constraint so that the fastest path in the design must meet hold time of the latch flipflop.

If a design fulfills both setup and hold constraints, the design is said to have achieved timing closure.

There are four categories of paths for static timing analysis:

1. I2O - input to output
2. I2R - input to register
3. R2R - register to register
4. R2O - register to output

[The Ultimate Guide to Static Timing Analysis (STA) - AnySilicon](https://anysilicon.com/the-ultimate-guide-to-static-timing-analysis-sta/)