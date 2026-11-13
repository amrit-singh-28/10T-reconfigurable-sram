# Reconfigurable 10T SRAM for Energy-Efficient CAM Operation and In-Memory Computing

## 1. Project Overview

This project implements and analyzes a **reconfigurable 10T SRAM architecture** capable of performing conventional memory operations, Content Addressable Memory (CAM) searches, and In-Memory Computing (IMC).

The design is based on the reference paper:

> Z. Zhang et al., "Reconfigurable 10T SRAM for Energy-Efficient CAM Operation and In-Memory Computing," *IEEE Transactions on Very Large Scale Integration (VLSI) Systems*, Vol. 33, No. 4, pp. 1065–1072, April 2025.

The main objective is to implement the proposed 10T SRAM architecture in Cadence Virtuoso and verify its functionality through transistor-level simulations.

The implementation was carried out at three levels:
* Single 10T SRAM cell
* 2×2 SRAM array
* 16×16 SRAM array

The 16×16 array was used for functional verification, process corner analysis, temperature analysis, Monte Carlo simulations, and energy measurements.

---

## 2. Design Architecture

The proposed 10T SRAM cell consists of:

* **6T SRAM core:** Stores the data using cross-coupled inverters and access transistors.
* **Compute section:** Four additional transistors (N5–N8) provide the read, compute, and CAM functionality.
* **Decoupled read ports:** Separate row-wise and column-wise read operations.
* **Match line (ML):** Used for detecting matches and mismatches during BCAM searches.

An important feature of this architecture is the **self-suppressed match-line discharge mechanism**. 

The high-threshold-voltage transistor N8 provides feedback during match-line discharge. As the match-line voltage decreases, the effective gate-to-source voltage of N8 also decreases, slowing down the discharge and preventing the match line from reaching 0 V. This helps reduce the energy consumed during CAM searches.

---


All schematics and simulation testbenches are organized inside the Cadence Virtuoso library.

### Library Information

| Parameter | Details |
| :--- | :--- |
| **Library Name** | `project_helper` |
| **Technology** | UMC 28 nm |
| **Design Type** | Transistor-level schematic |
| **Simulator** | Spectre |

### Schematic Locations

| Design Block | Cadence Library | Cell Name |
| :--- | :--- | :--- |
| Single 10T SRAM Cell | `project_helper` | `10T_SRAM_Cell` |
| 2×2 SRAM Array | `project_helper` | `10T_SRAM_2_2` | 
| 16×16 SRAM Array | `project_helper` | `10T_SRAM_32_32` | 
| Driver Circuit | `project_helper` | `driver` | 
| Sense Amplifier | `project_helper` | `sense_amplifier` | 

---

## 3. Operations Implemented

The 10T SRAM architecture was verified for five major operations.

### 3.1 Write Operation
* Data is written into the selected SRAM row.
* WWL is activated.
* WBL and WBLB carry complementary input data.
* The cross-coupled inverter pair stores the data.  
**Measured Energy:** 46.6 fJ/bit

### 3.2 Row-wise Read
* The selected row is activated using RWL.
* RBL and RBLB are precharged.
* The stored data determines the differential bit-line discharge.
* A differential sense amplifier detects the stored value.  
**Measured Energy:** 26.52 fJ/bit

### 3.3 Column-wise Read
* RWL lines are kept low.
* RBL and RBLB of the selected column are driven.
* The HBL voltage depends on the stored data.
* The output is detected using a reference voltage ($V_{\text{REF}} = V_{\text{DD}} - 100\text{ mV}$).
* This operation also enables matrix transposition by writing data row-wise and reading it column-wise.  
**Measured Energy:** 7.76 fJ/bit

### 3.4 Binary CAM (BCAM) Search
* RBL and RBLB act as search lines (SL and SLB).
* The match line is precharged high.
* If the stored data matches the search data, the match line remains high.
* If a mismatch occurs, the match line discharges via the self-suppressed discharge mechanism, which limits the voltage swing.  
**Measured Energy:** 0.64 fJ/bit/search

### 3.5 In-Memory Computing (IMC)
* Two selected rows are activated simultaneously using their respective RWL signals.
* The bit-line discharge pattern depends on the stored operands.
* Two single-ended sense amplifiers resolve the computation result.
* This allows Boolean operations to be performed directly within the memory array without transferring operands to an external processing unit.  
**Measured Energy:** 25.92 fJ/bit

---

## 4. Simulation and Analysis

The following analyses were performed on the 16×16 SRAM array:

1. **Functional Verification:** Verified all five core operations (Write, Row-wise read, Column-wise read, BCAM search, and In-memory logic computation).
2. **Process Corner Analysis:** Simulated across five process corners (TT, FF, SS, FS, SF) to measure match-line delay.
3. **Temperature Analysis:** Evaluated at $-40^\circ\text{C}$, $27^\circ\text{C}$, and $87^\circ\text{C}$ to examine the temperature dependency of the high-$V_{\text{th}}$ N8 transistor and match-line voltage.
4. **Monte Carlo Analysis:** Observed match-line discharge voltage variations to evaluate the robustness of the self-suppressed discharge mechanism against device variations.
5. **Mismatch Analysis:** Evaluated BCAM search under varying mismatch counts ($0, 2, 4, 8,$ and $16$ mismatches for a 16-bit row).
6. **Energy Analysis:** Quantified energy consumption for all five operations at the TT process corner.

---

## 5. Results and Observations

### 5.1 Energy per Operation

| Operation | Energy |
| :--- | ---: |
| **Write** | 46.6 fJ/bit |
| **Row Read** | 26.52 fJ/bit |
| **Column Read** | 7.76 fJ/bit |
| **In-Memory Logic** | 25.92 fJ/bit |
| **BCAM Search** | 0.64 fJ/bit/search |

* **Key Takeaways:**
  * The write operation has the highest energy consumption.
  * Column read consumes approximately **3.4 times less energy** than row read.
  * BCAM search is approximately **40 times more energy-efficient** than in-memory logic computation.

### 5.2 Match-Line Discharge Analysis

| Number of Mismatches | ML Voltage |
| ---: | ---: |
| **0** | 930.8 mV |
| **2** | 307.5 mV |
| **4** | 279.0 mV |
| **8** | 252.4 mV |
| **16** | 227.0 mV |

* **Observations:**
  * The match line remains at $\approx 0.93\text{ V}$ when all bits match.
  * Increasing the mismatch count reduces the match-line voltage, but **even under the all-mismatch condition (16 mismatches), the match line does not discharge to 0 V**.
  * A sensing margin of approximately $623\text{ mV}$ is observed between the match and weakest mismatch cases, verifying the self-suppressed match-line discharge mechanism.

### 5.3 Process Corner and Temperature Analysis
* The match-line voltage follows the ordering $\text{SS} > \text{SF} > \text{TT} > \text{FS} > \text{FF}$ at every simulated temperature.
* Slow NMOS process corners maintain a higher match-line voltage.
* Match-line voltage decreases by approximately $83\text{--}91\text{ mV}$ as temperature increases from $-40^\circ\text{C}$ to $87^\circ\text{C}$.
* The highest observed voltage is $\approx 435\text{ mV}$ at $\text{SS}$ and $-40^\circ\text{C}$, while the lowest is $\approx 218\text{ mV}$ at $\text{FF}$ and $87^\circ\text{C}$ (high-$V_{\text{th}}$ N8 implementation).

### 5.4 Match-Line Delay

| Process Corner | Reference Paper (ns) | Our Work (ns) |
| :--- | ---: | ---: |
| **FF** | 0.53 | 0.63 |
| **FS** | 0.58 | 0.72 |
| **TT** | 0.73 | 0.89 |
| **SF** | 0.87 | 0.99 |
| **SS** | 0.91 | 1.05 |

* **Observations:** $\text{FF}$ exhibits the lowest delay, whereas $\text{SS}$ exhibits the highest. Our implementation follows the same trend as the reference paper with approximately **14–24% higher delay** across corners.

### 5.5 Comparison with Reference Paper

| Metric | Reference Paper | Our Work |
| :--- | ---: | ---: |
| **Row Read Energy** | 22.5 fJ/bit | 26.52 fJ/bit |
| **Column Read Energy** | 19.2 fJ/bit | 7.76 fJ/bit |
| **Logic Energy** | 29.2 fJ/bit | 25.92 fJ/bit |
| **BCAM Energy** | 1.05 fJ/bit/search | 0.64 fJ/bit/search |
| **TT ML Delay** | 0.73 ns | 0.89 ns |
| **SS ML Delay** | 0.91 ns | 1.05 ns |

* Our implementation demonstrates **lower energy consumption** for column read, in-memory logic, and BCAM search compared to the reference paper.
* The higher match-line delay in our work is attributed to differences in array sizing, parasitic bit-line/match-line capacitances, PDK differences, supply voltages, and transistor sizing.

---

## 6. Overall Conclusion

The project successfully demonstrates the functionality of a reconfigurable 10T SRAM architecture supporting conventional SRAM operations, column-wise read, BCAM search, and in-memory computing. 

Simulations successfully verify the self-suppressed match-line discharge mechanism across process corners, temperature variations, and Monte Carlo analyses. The 16×16 implementation achieves standout efficiencies, reaching **0.64 fJ/bit/search** for BCAM and **25.92 fJ/bit** for in-memory logic computation, highlighting the potential of reconfigurable SRAM architectures for energy-efficient memory-centric computing.

---

## 7. Team Members & Credits

* **Singh Amritkumar**
* **Yash Choudhary**

* **Institute:** Indian Institute of Technology Gandhinagar  
* **Course:** VLSI Design  
* **Faculty:** Prof. Joycee M. Mekie  

---

## 8. References

1. Z. Zhang et al., "Reconfigurable 10T SRAM for Energy-Efficient CAM Operation and In-Memory Computing," *IEEE Transactions on Very Large Scale Integration (VLSI) Systems*, Vol. 33, No. 4, April 2025.