
# General idea

Comparative study of three high-resolution digital phase measurement methods for sub-nanosecond synchronization over Ethernet networks. It is implemented in FPGAs.

1. Digital Dual Mixer Time Difference (DDMTD)
2. Time-to-Digital Converter (TDC)
3. PLL-based Counter (PLLCTR)

These circuits are evaluated in terms of accuracy, resolution and implementation complexity. Measurements were validated using a calibrated 12 GHz oscilloscope.

**Precision Time Protocol** (PTP) as specified in the IEEE 1588-2008 standard has become the primary means for highly accurate time transfer and clock synchronization in networked applications. PTP has proven to be capable of synchroniying two devices well below 5 ns.

5G and future telecom technologies require increasingly tight synchronization for all base stations.

The ITU published several PTP profiles to cover frequency transfer over standard networks, time transfer over a network with partial PTP support and time transfer over networks with full PTP support.

Applications requiring sub-ns synchronization accuracies had to deploy and maintain a dedicated time and frequency transfer infrastructure rather than using a single Ethernet data and time transfer. Large radio telescope arrays like the Square Kilometre Array or large particle accellerator facilities like the Large Hadron Collider at CERN are just two such examples. 

Reference: "Comparative Analysis of High-Resolution Phase Measurement Principles for Picosecond Clock Synchronization", Roland Höller, Nikolaus Kerö, Sani Sarcevic, 2025 International Symposium Precision Clock Synchronization for Measurement, Control, and Communication (ISPCS), 2025 IEEE