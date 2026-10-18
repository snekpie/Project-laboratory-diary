
Precise measurement of *time elapsed between two events* or *time pulse* is an age-old requirement of many science and engineering applications. Time-to-digital converted (TDC) is the one that converts the incoming *time pulse* to its digital equivalent. 

*Thought: doesn't this make TDC some sort of analog-to-digital converter?*

Other names for TDC: time counter, time interval measurement, stop watch for time, time quantization (*ah yes, I remember this from measurement technology, this is used for analog-to-digital-conversion*), time interval meter (TIM) and time digitizer. 

Time model signal processing allows designers to make fully digital architectures. Advantages of fully digital integrated circuits (ICs) compared to the mixed-signal Integrated Circuits are with respect to parameters like compactness, silicon area, power, robustness against noise and coupling.

TDC has been deployed as a key component in some of the applications, namely, ADPLL, ADDPLL, clock and data recovery circuit (CDR), time-over-threshold (TOT) positron emission tomography (PET), jitter measurement, time-of-flight (TOF) laser radar, TOF PET scanner, silicon photomultiplier SiPM PET, Light Detection and Randing (LiDAR), digital storage oscilloscope,...

A study on TDCs describes the motivation from the *amplitude/voltage* domain to the *time* domain.

Similar to ADC, there are some parameters to be considered for TDC characterization. Resolution is one of the main parameters, which indicate smallest *time* pulse that TDC can quantize. Measurement range, nonlinearities like INL (integral nonlinearity) and DNL (differential non-linearity) *Thought: we learned about this in laboratory 2.*, conversion speed, PVT (process, voltage, temperature) effects, and dead time are some other parameters to be noted.

![[Wiley-time-to-digital-converter-block-diagram.png]]

### Classification

Wang et al. classified TDCs as direct and indirect measurement techniques. In the direct method, time is measured with a relative reference clock or delay element, wherein indirect method time is quantized to its equivalent voltage and then digitized using ADC. In a broad sense, direct method mode is called as *Time* mode technique and indirect methods can be classified as *amplitude* mode methods.

![[Classification of TDCs Wiley.png]]

#### Counter - the first TDC

Counter is the simplest and most reliable way of time measurement. The conceptual diagram of counter as TDC is depicted as following:

![[counter as TDc.png]]

Resolution depends on the frequency of the clock input. For example, if the counter is driven bt 1 MHz clock, the resolution will be 1 us. The range is decided by the number of bits of the counter. The measurement range can be doubled just by increasing one flipflop in the counter. It is difficult to enhance the resolution because of the bandwidth limitations of the circuit components and/or PCB. 

*Thought: I guess the bandwidth of the FPGA I have would be limited by the HFOSC in [[iCE40 UltraPlus Family Data Sheet Notes]]*

However, the clock can be easily multiplied using the phase-locked-loop (PLL) or DLL (delay-locked loop) inside the IC.

*Thought: there is a sysCLOCK PLL inside my FPGA.*

Enhancing the resolution will reduce the measurement range. For the same range, the enhanced resolution requires more hardware.

Advantage of counter: simplicity and reliability. Most of the modern multistage conversion techniques employ counter TDC at the coarse stage.

Limitation of counter: resolution. For example, counter clock frequency should not exceed 500 MHz in a commercial FPGA. Secondary drawback is the error associated with measurement. \most of the time the input time pulse (Tm) will be asynchronous, hence ending up with measurement error. This equation represents possible errors in the measurement:

$T_m = D_{out}  \cdot T_{clk} + {\Delta}_{t1} - {\Delta}_{t2}$

This error can be minimized by sampling the same input multiple times and averaging the outputs.

##### Indirect measurement - amplitude domain: the first generation

The working principle of indirect measurement is nothing but time to voltage digital converter.

![[indirect measurement principle diagram.png]]

- **Time-to-voltage converter**. the first stage in the indirect measurement would be time-to-voltage converter (TVC) also known as time-to-amplitude converter. (TAC) Many works implemented TVC with their topologies keeping charge pumps in common. TVC output will be fed to ADC for final conversion.
- **Integrating ADC as TDC**. Well known single-slope and dual-slope integrating ADC with slight modifications can be used as integrating TDC. 
- **Pulse stretching/time amplification.** One of main limitations of indirect measurement and counter TDC is the resolution. Resolution can be enhanced by stretching the time pulse followed by conversion. 

#### Pulse stretching techniques

1. **Single-slope pulse stretching**. Method involves charging capacitor C with constant current during START and discharging slowly with small current. With constant current being smaller than the discharging current, the capacitor charges quickly (T) and discharges slowly (Tr). The overall output of the circuit is stretched pulse (T + Tr), which is proportional to T. 
2. **Dual-slope pulse stretching.** Pulse stretching can also be done using a dual-slope method.It is done the same way as single-slope, except stretching is done on both residue and residue'.
3. **Time to amplitude, amplify and ADC.** This technique involves two steps: converting time residues ${\Delta}_{t1}$ and ${\Delta}_{t2}$ to their equivalent voltage levels using TAX and second, amplifying these voltage levels to a stretch factor, followed by ADC.


##### Direct measurement technique - time domain: second generation

The advantage of time domain data converters can only be explored with direct measurement TDC architectures. The basic principle of any converter is to compare the input with a known reference value or fraction of a reference value.

##### Delay line TDC

It is the most popular because of its simplicity, low latency and reliability. In CMOS implementation, resolution of the TDC is equal to the delay of the bugger. In FPGA implementation, the delay line can be constructed using a carry chain. Length of the delay line can be decided by the required dynamic range.



Reference: "Time-to-digital convertes - A comprehensive overview", Mahantesh P. Mattada, Hansraj Guhilot, International Journal of Circuit Theory and Applications, Volume 49, Issue 3, pages 535-908 [Time‐to‐digital converters—A comprehensive review](https://onlinelibrary.wiley.com/doi/epdf/10.1002/cta.2936)

### Applications of TDCs

#### TDCs in FPGAs

TDCs implemented on Field-Programmable Gate Arrays (FPGAs) have become increasingly prevalent across a wide range of scientific and engineering disciplines, such as high-energy physics experiments, autonomous driving, robotic navigation, medical imaging, owing to their cost-effectiveness, high precision and rapid development cycles. 

Analogous to how an Analog-to-Digital Converter (ADC) quantizes signal amplitude into digital representation, a **Time-to-Digital Converter (TDC) digitizes time intervals between two pulses**. Both converters share fundamental performance metrics, including resolution, dynamic range, Differential Nonlinearity, Integral Nonlinearity, and dead time. 

Their implementation spans multiple scientific and engineering domains requiring precise temporal measurements:
- in high-energy physics experiments like those at Large Hadron Collider, FPGAs paired with microchannel photomultipliers enable precise particle arrival time measurements for trajectory reconstruction and energy distribution analysis, leveraging their high precision and real-time processing capabilities.
- LiDAR systems in autonomous vehicles and robotic navigation employ FPGA-TDCs to enhance spatial resolution and system reliability through improved time-of-flight measurements.
- Medical imaging modalities such as Positron Emission Tomography (PET) benefit from FPGA-TDCs superior time resolution and multi-channel capabilities for accurate photon arrival time detection.

**Resolution** denotes the minimum measurable time interval, while practical measurement precision is typically characterized by the standard deviation of repeated time-interval measurements.

Reference: Chen,R.;Chen,P.;Li,K.; Liu, H.HeterogeneousTapped Delay-Line Time-to-Digital Converter on Artix-7FPGA.Sensors2025,25,2923. https://doi.org/10.3390/s25092923