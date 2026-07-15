<a id="top"></a>
<div align="center">

# DDR5 Overclocking Guide

In-depth reference for pushing DDR5 memory.

![Memory](https://img.shields.io/badge/Memory-DDR5-blue)
![Level](https://img.shields.io/badge/Level-Advanced-red)
![Status](https://img.shields.io/badge/Status-Work%20in%20Progress-yellow)

</div>

<br/>

> [!CAUTION]
> This guide pushes DRAM and platform voltages above default specification. Pushing SA or DRAM voltage too far can degrade your IMC or DIMMs over time. Even staying within the ranges in [Voltage and Timing Ranges](#voltage-and-timing-ranges) does not guarantee absolute safety.

> [!WARNING]
> This guide is in its early phase. Additions or corrections are appreciated, contact me on Discord gorazyx.

<summary><b>Table of Contents</b></summary>
<br/>

- [Baseline Memory OC](#baseline-memory-oc)
- [Overclocking](#overclocking)
- [Voltages](#voltages)
- [Voltage and Timing Ranges](#voltage-and-timing-ranges)


<a id="baseline-memory-oc"></a>
## Baseline Memory OC

| Setting | Value | Notes |
|---|---|---|
| CPU Core/Ring Ratio | 47/44 | Remove CPU OC instability as a variable at first. |
| VRM VCore/SVID VCore/SVID Cache | 1.2V | Prevent degrading. Final OC should never touch 1.35V+ anyway. |
| Load-Line | LLC6 | LLC8 (no vdroop) if properly cooled. |
| Unlimited ICCMAX | Disabled | Prevent degrading. |
| ICCMAX | 400A | Prevent degrading. |
| IA VR Voltage Limit | 1600 | Prevent degrading. |
| VCCSA | 1.15V | Needs full train + cold boot when changing. VCCSA in 5mV steps is very important — has a sweet spot. |
| VDD/VDDQ | 1.45/1.45V | Puts PMIC in OC-Mode (1.425V+ → different thermal management + voltage deltas). |
| IVR VDDQ TX | 1.2V | Needs a sweet spot; optimal value depends on board/CPU/DIMM quality and IC type (A-Die vs M-Die). |
| XMP | XMP I | |
| Gear Mode | 2 | |
| DRAM Reference Clock Ratio | Auto | Changing this from anything but Auto bugs training and needs a full CMOS reset. You can set 100:100 but never change it again. |
| Maximus Tweak | Mode 2 | Trains Auto timings more aggressively. Mode 1 trains for compatibility. |
| DRAM Frequency | 7200 | |

### Timings

| Timing | Value | Notes |
|---|---|---|
| tCL | 38 | 36 when pushing higher VDD (1.55V+) at 8000+. Requires proper cooling. |
| tRCD | 54 | Fail-safe value; will go lower after. |
| tRCDW | 54 | Fail-safe value; will go lower after. |
| tRP | 54 | Fail-safe value; will go lower after. |
| tRAS | 82 | Set tRAS = tRCD 54 + tRTP 24 + 4 = tRAS 82. |
| Command Rate | 2N | |
| tREFI | 32767 | tREFI = 8192*N - 1 where N is an integer. |
| tRFC / tRFC1 / tRFC2 | 800 | Latency (ns) = tRFC * 2000 / Data Rate. M-Die floor is much higher than A-Die floor because of density — 160ns for A-Die / 190ns for M-Die. |
| tRFCpb / tRFCsb | 672 | Can set to tRFC - 30ns. |
| tWR | 72 | Don't floor this one too hard. Most firmware sets 48 as the limit, but APEX can set it to 24. These are way too tight anyway. |
| tRRD_L | 12 | 8 as floor for M-Die. 4 can be tested for A-Die. |
| tRRD_S | 8 | 8 as floor for M-Die. 4 can be tested for A-Die. |
| tWTR_S | 8 | 4 as the absolute floor; can go as low as 2. |
| tWTR_L | 24 | 8 as the absolute floor; can go as low as 6. |
| tRTP | 24 | 12 is the absolute floor for DDR5. |
| tFAW | 48 | 32 recommended as absolute floor. 16 (not recommended) worth trying on A-Die. |
| tCWL | 36 | tCWL = tCL - 2. Can go lower, e.g. tCWL = tCL - 4. Also changes timing rules for tWRRD_SG/DG. |
| tRDRD_SG | 16 | |
| tRDRD_DG | 8 | |
| tRDWR_SG | 21 | 22 if 8400+ |
| tRDWR_DG | 21 | 22 if 8400+ |
| tWRRD_DG | Auto | Leave Auto and let the board train via tWTR_S. If your board cannot directly change tWTR_S, set tWTR_S Auto and reduce tWRRD_DG until you hit the tWTR_S target. |
| tWRRD_SG | Auto | Leave Auto and let the board train via tWTR_L. If your board cannot directly change tWTR_S, set tWTR_L Auto and reduce tWRRD_SG until you hit the tWTR_L target. |
| tWRWR_SG | 16 | 32, 38, 40 can be tested depending on other timings — only for fine-tuning at the end. |
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
| Memory Context Restore | Disabled | Higher training times, but worth it. |
| DDR Runtime Training Reduction | Disabled | Higher training times, but worth it. |
| BankSwapMode | Swap APU | |
| Latency Under Load | Disabled | |
| Nitro | 1/2/0 for 1:1 | 1/3/1 for 2:1 |

<p align="right"><a href="#top">Back to top</a></p>

---

<a id="overclocking"></a>
## Overclocking

### Stress Tests
| Stress test | Time to run |
|---|---|
| y-cruncher VST/VT3 | 2-4 hours |
| Karhu | 400%+ coverage |
| TM5 anta777 | 3–5 passes |
| Memtest86+ | 2 passes |
| HCI Memtest | 200%+ coverage |

### Overclocking Procedure

> [!IMPORTANT]
> Always aim to change only **one variable at a time**. The actual overclocking process is boring, repetitive, and takes a long time. If you are serious about overclocking, approach it step-by-step like this — otherwise you'll spend months on your OC and learn nothing without any progress.

**Step 1 — Push frequency**
1. Push frequency.
2. Increase VDD/VDDQ if there is headroom.
3. Repeat until you reach the max frequency you reliably POST.
3. Stress test for 5-10 minutes of y-cruncher VST. 
4. Raise VDD/VDDQ if it errors, reduce frequency if no VDD/VDDQ headroom. 

**Step 2 — Tighten primaries**
1. Tighten primaries one at a time.
2. Increase VDD/VDDQ if there is headroom.
3. Repeat until you either can't POST or can't stabilize a specific timing.
4. Adjust or raise VDD/VDDQ or reduce frequency.

**Step 3 — Tighten secondaries**
1. Tighten secondaries.
2. Adjust VDD first, then VDD2/IMC Voltage. (VDDQ and IVR VDDQ TX only affect a small subset of secondaries.)
3. Repeat until you either can't POST or can't stabilize a specific timing.

**Step 4 — Tighten tertiaries** 
1. Tighten tertiaries.
2. Increase VDD if there is headroom.
3. Repeat until you either can't POST or can't stabilize a specific timing.

**Step 5 — Finalize**
1. Repeat the steps from before, depending on what your goal is.
2. If you are stuck, start adjusting IVR VDDQ TX, VCCSA, and VDD2/IMC Voltage individually 
3. Once you are satisfied with your overclock, run every stress test for as long as reasonable.

<p align="right"><a href="#top">Back to top</a></p>

---


<a id="voltages"></a>
## Voltages
> [!TIP]
> Go overkill on cooling and raise voltages only as much as you *need*.

### VDD
Primary supply voltage for the DRAM die. The PMIC generates it on the DIMM. 
Increasing it helps push igher frequency and almost every timing scales with it.
Above 1.55V+ you need to ensure proper cooling.

### VDDQ
Powers I/O circuitry on the DRAM die. The PMIC generates it on the DIMM. Related to DQ/DQS signal quality. 
Usually fine at VDDQ = VDD up until 1.55V. Depending on ICs and motherboard, a delta (50–100mV) above 1.55V VDD (so 1.50V VDDQ for 50mV delta) helps reduce heat generated on the I/O.

### VDD2
A sub-domain of VCCSA (System Agent). This rail powers the digital logic that runs training. 
Do not undervolt it. Doing so creates issues with training. Push this voltage decently.

### IVR VDDQ TX
This rail powers the CPU PHY transmit drivers. It affects CPU write-path signal integrity. 
Above 7800+ MT/s, CPU PHY drive signal integrity starts becoming a limiting factor — this voltage that directly relates to write-path eye opening. 
Raising it too much creates signal integrity issues which occur fast starting from 1.25V upwards. The ideal value is a sweet-spot.

### VCCSA
> [!WARNING]
> Don't overdrive SA voltage. It degrades 13th/14th-gen IMCs quickly and is a common cause of instability — treat 1.30V as a reasonable upper limit.
Mainly a supporting rail — affects memory training, interfaces around the IMC, and timing calibration/initialization logic. 
Configuring it properly mainly helps with booting/training consistently, and matters more when trying to boot at higher frequencies. 
If VCCSA is too low or misconfigured, you cannot boot at all. The ideal value is a sweet-spot.

Raising voltages creates stronger signal and let's transistors switch faster, but creates two main problems:

1. **Heat** changes impedance, which distorts the signal.
2. **Faster signal edge** create more over- and undershoot.

The first problem is solved with better cooling. 
The second is solved by increasing termination resistances — but that drops signal strength across the resistance, which creates heat.

<p align="right"><a href="#top">Back to top</a></p>

---

<a id="cooling"></a>
## Cooling

The resistance of copper increases directly proportional to temperature increases, which worsens signal integrity. The signal ideally wants the same resistance across the entire signal path — any inconsistency in impedance along the path acts like a "speed bump," and part of the signal reflects back to the source and interferes with the next signal. This reflection ripple adds on top of the signal and distorts it.

At very low frequency, some noise still allows the receiver to differentiate a "1" from a "0" voltage level. As frequency increases, reducing noise becomes far more important, because signals become incredibly sensitive to noise. (The full reason why is outside the scope of this guide.) Key takeaways:

- Cooling is incredibly important.
- Higher frequencies become very sensitive to even small noise.

You need especially good cooling to get decent results, especially on DDR5.

> [!TIP]
> For most people, pointing a fan directly at the RAM is enough — make sure the PMIC gets direct airflow, especially at high VDD/VDDQ. It's also worth cooling your CPU via direct-die + liquid metal (make sure you have a decent enough sample to do so). RAM water-cooling also exists.

<p align="right"><a href="#top">Back to top</a></p>

---

<div align="center">

</div>

<a id="refresh"></a>
## Refresh

DRAM cells leak charge over time and need to be periodically refreshed. They leak even faster at higher temperature, so leave margin when validating to ensure stability.

### tRFC

tRFC is the duration of the refresh. tRFC1 can be set to tRFC2 — JEDEC just defines tRFC2 as an aggressive spec, and either tRFC1 or tRFC2 is used. However, you have to set tRFCsb/tRFCpb separately. Make sure SBR is enabled in BIOS.

| Parameter | Description |
|---|---|
| tRFC1 | Conservative all-bank refresh (REFab) |
| tRFC2 | Aggressive all-bank refresh (REFab) |
| tRFCsb | Per bank-group refresh (REFsb) |

**JEDEC reference values** (tRFC1 \| tRFC2 \| tRFCsb):

| Die | tRFC1 | tRFC2 | tRFCsb |
|---|---|---|---|
| 16Gb A-Die | 295 ns | 160 ns | 130 ns |
| 24Gb M-Die | 410 ns | 220 ns | 205 ns |

**Main takeaways:**
- A-Die can go much lower with tRFC than M-Die.
- Only break spec by 10–20% as your limit — going too tight isn't worth the subtle bit-level errors from overtightening.
- Refreshes are incredibly temperature sensitive. tRFC and tREFI sensitivity cannot be overstated — set them reasonably.

### tREFI

tREFI is the average refresh interval. Since DDR5 introduced postponed refreshes, setting tREFI is a bit more tricky and you can get inconsistent performance regression or instability. It is also temperature sensitive for the same reason outlined above.

> [!TIP]
> Start with ~32000 for tREFI, and only if you have sufficient cooling, try ~65000. It's not worth going above 100k — there's barely any benefit to be had and plenty that can go wrong.

<p align="right"><a href="#top">Back to top</a></p>

---

## Notes

### Technical Notes

- DDR5's on-die ECC can correct some errors.
- You are most likely limited by your current CPU bin and/or thermals.
- DDR5 training is not deterministic — validate thoroughly and retry multiple times.
- DDR5 is very temperature sensitive, especially at higher speeds and relating to refresh timings.
- Proper DDR5 OC is complex because you have to validate across multiple states: cold boots, warm boots, retrains after failed training, and stress tests. This is why it may take months before you land on your "final" overclock.
- Every stress test/benchmark uses a different workload. Some may have mixed workloads, but real-world apps/games load your system in a very dynamic and heavy way — this is why you might pass some stress tests but still get random crashes.

### Tips

- Log every change you make with notes by hand.
- Increment one atomic step at a time.
- Save as many OC profiles as possible and name them properly.
- Note down Q-codes that happen during training.
- Validate that the board programmed your timings/BIOS values properly with tools like CPU-Z, HWiNFO, or Intel MRC.

<p align="right"><a href="#top">Back to top</a></p>

---

<a id="voltage-and-timing-ranges"></a>
## Voltage and Timing Ranges

> [!NOTE]
> These ranges will vary depending on hardware/frequency/cooling solution/etc. They're a general idea of what's typical and when you're approaching certain voltage/timing limits.

### Voltage Ranges

| Rail | Range | Notes |
|---|---|---|
| VDD (DRAM core) | 1.45–1.65V | 1.55V+ needs active cooling. 1.60V+ needs even more exotic cooling, like water-cooling. |
| VDDQ (DRAM I/O) | 1.45–1.60V | Especially at higher VDD (1.55V+), it makes sense to leave VDDQ at least 50mV or more below VDD. |
| VDD2 (IMC/CPU) | 1.45–1.60V | Very important rail for training/booting. |
| IVR VDDQ TX (CPU) | 1.20–1.45V | Above 8200+ especially, this voltage becomes interesting for signal integrity. Has a sweet spot. |
| VCCSA (SA) | 1.15–1.30V | Very fine sweet spot above 8200+. 1.20V might train sometimes, 1.24V might train excellent, 1.27V might regress again. |
| VPP (wordline boost) | 1.70–2.00V | Only change from 1.80V if all other voltages didn't help and you're certain the limit is row-activation related. |

### Primaries Range

| Timing | Range | Notes |
|---|---|---|
| tCL | 24–42 | Scales very well with increased VDD. Can go much lower than tRCD and tRP. |
| tRCD | ??–54 | By far one of the biggest bottlenecks at higher frequencies. If stuck, try adjusting in this order: VDD → IMC/VDD2 → VDDQ → IVR VDDQ TX → VPP. |
| tRCDWR | ??–54 | Can drop further. |
| tRP | ??–54 | |
| tRAS | ??–80 | |
| tRC | — | |
| tCWL | 26–42 | |
| Command Rate | 1–2 | |

### Secondaries Range

| Timing | Range | Notes |
|---|---|---|
| tRFC (tRFC1, tRFC2) | 160–400 ns | 160ns floor for A-Die, 190ns floor for M-Die. Many go lower, but it's a risk below that point. |
| tRFCsb (= tRFCpb) | 130–205 ns | Roughly tRFC - 30ns. |
| tREFI | 32767–131071 | Not recommended to go above 65535 without a strong reason. |
| tRRD_S | 04–12 | 4 is very extreme. |
| tRRD_L | 04–12 | |
| tFAW | 16–48 | |
| tWTR_S | ??–24 | |
| tWTR_L | ??–64 | |
| tRTP | 12–32 | |

### Tertiaries

<details>
<summary>WIP</summary>

- tRDRD_sg, tRDRD_dg, tRDRD_dr, tRDRD_dd
- tWRWR_sg, tWRWR_dg, tWRWR_dr, tWRWR_dd
- tRDWR_sg, tRDWR_dg, tRDWR_dr, tRDWR_dd
- tWRRD_sg, tWRRD_dg, tWRRD_dr, tWRRD_dd

</details>

<p align="right"><a href="#top">Back to top</a></p>
