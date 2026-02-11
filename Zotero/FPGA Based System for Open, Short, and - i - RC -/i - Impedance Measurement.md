---
citekey:
  "{ citekey }":
status:
tags:
  - literature-note
---

# FPGA Based System for Open, Short, and <i>RC</i> Impedance Measurement

**Authors:** K.E. Newman
**Year:** 2010
**Journal:** IEEE Transactions on Advanced Packaging
**Zotero Link:** [Open in Zotero]()

## Abstract
This paper describes a time-based digital test system that may be used to detect opens, and shorts, as well as measure resistive and capacitive impedance of interconnect networks. The speciﬁc test implementation is customized through a ﬁeld programmable gate array (FPGA). The use of an FPGA allows for reconﬁguration of the test for many different interconnect veriﬁcations. The current progress of this system is demonstrated and experimental measurements are provided.

---
## Zotero Notes & Highlights

> time-based digital test system that may be used to detect (p. 147)

---
> opens, and shorts, as well as measure resistive and capacitive impedance of interconnect networks. (p. 147)

---
> customized through a field programmable gate array (FPGA) (p. 147)

---


---
> Embedded passives (p. 147)

---


---
> device under test (DUT) (p. 147)

---
> This information can then be used to determine if the component or connection is out of normal operating ranges and should either be repaired, replaced, or ignored. (p. 147)

---
> may be updated as the system configuration is modified (p. 147)

---
> The JTAG scan chain may be utilized to alter the operation of the embedded FPGA component during test so that connectivity may be monitored (p. 147)

---
> If errors are found in the external network, then the FPGA can be updated to overcome any failures in the external devices. (p. 147)

---
> test system so that reliable detection of opens and shorts are performed along with measurements of resistance-capacitance (RC) values of embedded (p. 147)

---
> passive devices. (p. 147)

---


---
> Each test channel has driver and receiver capability which is monitored and configured by a test controller. (p. 147)

---
> Only one driver should be active per interconnect network (p. 147)

---
> determine if the bare substrate network is connected by measuring the time required for a signal to propagate from a transmitter to a receiver (p. 147)

---
> charging and discharging signals are transmitted by the active driver and monitored by the receivers. (p. 147)

---
> has RC components, there will be a measurable delay between the logic patterns for the transmitter and the receiver. (p. 147)

---
> The accuracy of the measurement can be adjusted through modifications to the time step of the digital portion of the tester as well as the RC components selected for the tester interface. (p. 147)

---


---
> Series resistors are used to shield the driver from any over current if a short occurs between multiple interconnect networks (p. 147)

---
> A pull down resistor is used to ensure a digital logic zero if the network is not driven due to an open circuit. (p. 147)

---
> selected for the desired RC charging time which is used to estimate the value of the unknown passive components. (p. 147)

---


---


---


---


---
> In order to determine the external network configuration for the tester, an equivalent circuit model must be approximated for the FPGA pin output (p. 148)

---
> A sample program is used to drive the output pin high and low across a measured resistance of 10.4 and 1 k to determine the analog characteristics of the output pin. (p. 148)

---
> measured as 0.27 V and 3.06 V (p. 148)

---
> it is determined that the current is not consisten (p. 148)

---


---
> an operational amplifier is placed on the output pin (p. 148)

---
> from the FPGA to create a constant current supply for the RC network. The op-amp circuit is configured as a buffer so and the output is connected directly to the inverting input. (p. 148)

---
> This modification shown in Fig. 4 provides a more stable (p. 148)

---
> output and is used for the remainder of the measurements. (p. 148)

---
> step response analysis (p. 148)

---


---


---


---


---


---


---


---


---


---


---


---


---


---


---


---
> can be simplified if is assumed to be very small compared to the other components in the network (p. 149)

---


---


---


---
> If the impedance of the network is zero, must be able to reach a digital logic one value when sends a logic one. (p. 149)

---
> the range of the output for a logic one is 3.6–3.0 V with a typical output of 3.0 V. The range for the detection of a logic one is minimum 2 V. (p. 149)

---


---


---


---


---


---
> solved to determine the range of measurable impedance values as a function of the fixed network values (p. 149)

---
> steady state RC values that can be detected (p. 149)

---
> An FPGA sampling clock is then selected that is greater than the charging rate for the desired range of values. (p. 149)

---
> Spice prediction of the delay values is limited because the internal configuration of the FPGA pin interface is not fully characterized. (p. 150)

---
> using the Verilog hardware design language (HDL) in a modular fashion (p. 150)

---
> test channel (channew) (p. 150)

---
> receiver (p. 150)

---
> transmitter (p. 150)

---
> using an RC substitution box (p. 150)

---
> 50-MHz clock (p. 150)

---


---


---


---
> sampling rate of 500 kHz to detect the charging of the RC network. (p. 150)

---
> Channel 1 sends Digital Data Out (p. 150)

---
> Channel 2 captures Digital Data In (p. 150)

---
> Voltage supply for the 741 is 5 V (p. 150)

---
> low value is ground (p. 150)

---
> with changing from 100 to 8 k . (p. 150)

---
