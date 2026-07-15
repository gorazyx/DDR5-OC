<a id="top"></a>
<div align="center">

# DDR5 Overclocking Guide

</div>

<br/>

> [!CAUTION]
> This guide pushes voltages above default specification. Pushing IMC/SA/CPU or DRAM voltage too far will degrade your IMC or DIMMs. Even staying within the ranges in [Voltage and Timing Ranges](#voltage-and-timing-ranges) does not guarantee safety.

> [!WARNING]
> This guide is still being made. Contact me on discord gorazyx for corrections or additions to the guide.

<details>
<summary><b>Table of Contents</b></summary>
<br/>

- [Baseline Memory OC](#baseline-memory-oc)
- [Overclocking](#overclocking)
- [Voltages](#voltages)
- [Voltage and Timing Ranges](#voltage-and-timing-ranges)

</details>

<a id="baseline-memory-oc"></a>
## Baseline Memory OC

| Setting | Value | Notes |
|---|---|---|
| CPU Core/Ring Ratio | 47/44 | |
| VRM VCore/SVID VCore/SVID Cache | 1.20V | 1.35V max. |
| Load-Line | LLC6 | LLC8 if properly cooled. |
| Unlimited ICCMAX | Disabled | |
| ICCMAX | 400A | |
| IA VR Voltage Limit | 1600mV | |
| VCCSA | 1.15V | Adjust VCCSA in 5mV steps above 8000+ MT/s. |
| VDD/VDDQ | 1.45/1.45V | |
| IVR VDDQ TX | 1.20V | Adjust in 5mV steps above 8000+ MT/s. Disable Vddq Training |
| XMP | XMP I | |
| Gear Mode | 2 | |
| DRAM Reference Clock Ratio | Auto | Changing this from Auto after setting frequency bugs training. You can set 100:100 but never change it again. |
| Maximus Tweak | Mode 2 | Mode 2 trains aggressive. Mode 1 trains compatible. |
| DRAM Frequency | 7200 | |

### Timings

| Timing | Value | Notes |
|---|---|---|
| tCL | 38 | |
| tRCD | 54 | tRCD is the hardest of the primaries to stabilize. |
| tRCDWR | 54 | |
| tRP | 54 | Keep tRCD=tRP at first. |
| tRAS | 82 | Set tRAS = tRCD + tRTP + 4. |
| Command Rate | 2N | |
| tREFI | 32767 | |
| tRFC / tRFC1 / tRFC2 | 200ns | Latency = tRFC * 2000 / Data Rate. |
| tRFCpb / tRFCsb | 170ns | Set to tRFC - 30ns. |
| tWR | 72 | 48 floor. APEX can program 24. |
| tRRD_L | 12 | |
| tRRD_S | 8 | |
| tWTR_S | 8 | |
| tWTR_L | 24 | |
| tRTP | 24 | 12 is floor for DDR5 |
| tFAW | 48 | 32 floor for M-Die. Try 16 floor on A-Die. |
| tCWL | 36 | tCWL affects tWRRD_SG/DG. |
| tRDRD_SG | 16 | |
| tRDRD_DG | 8 | |
| tRDWR_SG | 21 | 22 if 8400+ |
| tRDWR_DG | 21 | 22 if 8400+ |
| tWRRD_DG | Auto | Leave Auto and let the board train via tWTR_S. |
| tWRRD_SG | Auto | Leave Auto and let the board train via tWTR_L. |
| tWRWR_SG | 16 | |
| tWRWR_DG | 8 | |

### Advanced Voltages Tab

| Setting | Value |
|---|---|
| PMIC Voltages | Sync all PMICs |
| SPD HUB VLDO | 1.80V |
| SPD HUB VDDIO | 1.00V |
| VPP Voltage | 1.80V |
| VDD Switching Freq | 1.50 |
| VDDQ Switching Freq | 1.50 |
| VPP Switching Freq | 1.50 |
| Current Capability | 7.8750 |

### Miscellaneous Settings

| Setting | Value |
|---|---|
| MRC Fast Boot | Disabled |
| MCH Full Check | Auto |
| Retry Fail Count | 2 |
| RX-DFE | Auto |
| Fine Granularity Refresh Mode (FGR) | Auto |

### Notes for AMD

| Setting | Value | Notes |
|---|---|---|
| Memory Context Restore | Disabled | Enable it after finishing your OC. |
| DDR Runtime Training Reduction | Disabled | Higher training times for better training. |
| BankSwapMode | Swap APU | |
| Latency Under Load | Disabled | |
| Nitro | 1/2/0 for 1:1 | 1/3/1 for 2:1 |

<p align="right"><a href="#top">Back to top</a></p>

<a id="overclocking"></a>
## Overclocking

### Stress Tests
| Stress test | Time |
|---|---|
| y-cruncher VST/VT3 | 2-4 hours |
| Karhu | 400%+ coverage |
| TM5 anta777 | 3–5 passes |
| Memtest86+ | 2 passes |
| HCI Memtest | 200%+ coverage |

### Example Procedure

> [!IMPORTANT]
> Only change one thing at a time.

**Step 1: Frequency**
1. Push frequency.
2. Increase VDD/VDDQ if there is headroom.
3. Repeat until you reach the max frequency you reliably POST.
3. Stress test for 5-10 minutes of y-cruncher VST. 
4. Raise VDD/VDDQ if it errors, reduce frequency if no VDD/VDDQ headroom. 

**Step 2: Primaries**
1. Tighten primaries one at a time.
2. Increase VDD/VDDQ if there is headroom.
3. Repeat until you either can't POST or can't stabilize a specific timing.
4. Adjust or raise VDD/VDDQ or reduce frequency.

**Step 3: Secondaries**
1. Tighten secondaries.
2. Adjust VDD first, then VDD2/IMC Voltage. (VDDQ and IVR VDDQ TX only affect a small subset of secondaries.)
3. Repeat until you either can't POST or can't stabilize a specific timing.

**Step 4: Tertiaries** 
1. Tighten tertiaries.
2. Increase VDD if there is headroom.
3. Repeat until you either can't POST or can't stabilize a specific timing.

**Step 5**
1. Repeat the steps from before, depending on what your goal is.
2. If you are stuck, start adjusting IVR VDDQ TX, VCCSA, and VDD2/IMC Voltage individually 
3. Once you are satisfied with your overclock, run every stress test for as long as reasonable.

<p align="right"><a href="#top">Back to top</a></p>


<a id="voltages"></a>
## Voltages

> [!TIP]
> Overkill cooling and raise voltages as much as needed.

### VDD
Primary supply voltage for the DRAM die. The PMIC generates it on DIMM. 
Increasing it helps push higher frequency and almost every timing scales with it.
Above 1.55V+ you need to ensure proper cooling.

### VDDQ
Powers I/O circuitry on the DRAM die. The PMIC generates it on DIMM. Related to DQ/DQS signal quality. 
Usually fine at VDDQ = VDD up until 1.55V. Depending on ICs and motherboard, a delta (50–100mV) above 1.55V VDD (so 1.50V VDDQ for 50mV delta) helps reduce heat generated on the I/O.

### VDD2/IMC Voltage
A sub-domain of VCCSA (System Agent). This rail powers the logic that runs training. 
Push this voltage decently.

### IVR VDDQ TX
This rail powers the CPU PHY transmit drivers. It affects CPU write-path signal integrity. 
Above 7800+ MT/s, CPU PHY drive signal integrity becomes a limiting factor. This voltage directly relates to write-path eye opening. 
Raising it too much creates signal integrity issues. Adjust in 5mV steps.

### VCCSA
> [!WARNING]
> Don't overdrive VCCSA. It degrades 13th/14th-gen IMCs quickly.

VCCSA is mainly a supporting rail that affects training and timing initialization. 
Above 7800+ MT/s, adjust in 5mV steps.

<p align="right"><a href="#top">Back to top</a></p>

<a id="cooling"></a>
## Cooling

The resistance of copper is directly proportional to temperature. Signals want the same resistance across the entire signal path. Changes in resistance across the path causes reflections.
You need especially good cooling to get especially good results, especially on DDR5.

> [!TIP]
> Point the fan directly at the PMIC so gets direct airflow. This is especially important at high VDD/VDDQ. 
> It's worth cooling your CPU with liquid metal via direct-die. RAM water-cooling also exists.


<a id="refresh"></a>
## Refresh

DRAM cells leak charge over time and their data needs to be periodically refreshed. They leak even faster at higher temperature, so leave a big margin for refresh timings.

### tRFC

tRFC is the duration of the refresh. Just set tRFC1=tRFC2. tRFC1 is conservative, while tRFC2 is the aggressive spec. tRFCsb/tRFCpb is the refresh duration per bank.
Bcause of density, M-Die takes longer for the refresh to finish than A-Die.

**JEDEC reference values** (tRFC1 \| tRFC2 \| tRFCsb):

| Die | tRFC1 | tRFC2 | tRFCsb |
|---|---|---|---|
| 16Gb A-Die | 295 ns | 160 ns | 130 ns |
| 24Gb M-Die | 410 ns | 220 ns | 205 ns |


### tREFI

tREFI is the average refresh interval. Since DDR5 introduced postponed refreshes, misconfiguring tREFI gets you inconsistent performance or instability. It is very temperature sensitive.

> [!TIP]
> Start with ~32k for tREFI. If you have sufficient cooling, use ~65k. Never go above ~100k.

<p align="right"><a href="#top">Back to top</a></p>

## Notes

### Technical Notes

- DDR5's on-die ECC corrects some errors.
- You are limited by your current CPU bin and thermals.
- DDR5 is very temperature sensitive.
- You need to validate across multiple states: cold boots, warm boots, retrains after failed training, and stress tests. This makes DDR5 OC more complex than DDR4 OC.

### Tips

- Log every change you make with notes by hand.
- Increment one atomic step at a time.
- Save as many OC profiles as possible.
- Note down Q-codes that happen during training.

<p align="right"><a href="#top">Back to top</a></p>

<a id="voltage-and-timing-ranges"></a>
## Voltage and Timing Ranges

> [!NOTE]
> These ranges will vary depending on hardware/frequency/cooling solution/etc.

# Voltage Limits

| Voltage Rail | Safe | Air | Advanced | Notes |
|---|---|---|---|---|
| VDD (DRAM) | 1.50V | 1.55V | 1.65V+ | Most important rail to push. |
| VDDQ (DRAM) | 1.50V | 1.55V | 1.60V | At higher VDD (1.55V+), keep VDDQ at least 50mV below VDD. |
| VDD2/IMC Voltage (CPU) | 1.50V | 1.55V | 1.60V+ | Critical rail for training/booting. |
| IVR VDDQ TX (CPU) | 1.25V | 1.35V | 1.40V | Becomes relevant for signal integrity above 8200+ MT/s. Has a sweet spot. |
| VCCSA (CPU) | 1.20V | 1.25V | 1.30V | Very fine sweet spot above 8200+. Small increments (5-10mV) matter. 1.35V+ easily degrades the IMC. |
| VPP | 1.80V | 1.90V | 2.00V | Only adjust if all other rails are tuned and the limit is clearly row-activation related. |

# Primary Timings

| Timing | Safe | Tight | XOC | Notes |
|---|---|---|---|---|
| tCL | 9.5ns | 9ns| 8ns | Scales well with VDD. Can go lower than tRCD/tRP. |
| tRCD | 13ns | 12.5ns | 12ns | Will insanely bottleneck. |
| tRCDWR | 11ns | 10.5ns | 10ns | |
| tRP | 12.5 | 12ns | 11.5ns | |
| tRAS | tRCD + tCL + 2 | tRCD + tCL4 | tRCD + tCL | |
| tRC | tRAS + tRP | tRAS + tRP | tRAS + tRP | |
| tCWL | tCL | tCL − 2 | tCL − 4 | |
| Command Rate | 2N | 2N | 2N | 1N is only a thing way below 7000 MT/s. |

# Secondary Timings

| Timing | Safe | Tight | XOC | Notes |
|---|---|---|---|---|
| tRFC1/tRFC2 | 400ns | 240ns | 160-200 ns | 200ns for M-Die because of density. |
| tRFCsb (tRFCpb) | 300ns | 200 ns | 130-170ns | Roughly tRFC − 30 ns as a baseline. |
| tREFI | 32767 | 32767 | 65535 | I recommend staying below 65535. |
| tRRD_S | 12 | 8 | 4 | |
| tRRD_L | 12 | 12 | 8 | |
| tFAW | 48 | 32 | 16 | Must stay ≥ 4*tRRD_S. |
| tWTR_S | 12 | 8 | 4 | |
| tWTR_L | 32 | 24 | 8 | |
| tRTP | 24 | 12 | 12 | |


### Tertiaries

<details>
<summary>WIP</summary>

- tRDRD_sg, tRDRD_dg, tRDRD_dr, tRDRD_dd
- tWRWR_sg, tWRWR_dg, tWRWR_dr, tWRWR_dd
- tRDWR_sg, tRDWR_dg, tRDWR_dr, tRDWR_dd
- tWRRD_sg, tWRRD_dg, tWRRD_dr, tWRRD_dd

</details>

<p align="right"><a href="#top">Back to top</a></p>
