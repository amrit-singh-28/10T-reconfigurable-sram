# Reconfigurable 10T SRAM for Energy-Efficient CAM Operation and In-Memory Computing\*\*

## 1. Project Overview\*\*

This project implements and analyzes a \*\*\*\*reconfigurable 10T SRAM
architecture\*\*\*\* capable of performing conventional memory
operations, Content Addressable Memory (CAM) searches, and In-Memory
Computing (IMC).

The design is based on the reference paper:

> Z. Zhang et al., "Reconfigurable 10T SRAM for Energy-Efficient CAM
> Operation and In-Memory Computing," IEEE Transactions on Very Large
> Scale Integration (VLSI) Systems, Vol. 33, No. 4, pp. 1065--1072,
> April 2025.

The main objective is to implement the proposed 10T SRAM architecture in
Cadence Virtuoso and verify its functionality through transistor-level
simulations.

The implementation was carried out at three levels:

-   Single 10T SRAM cell

-   2×2 SRAM array

-   16×16 SRAM array

The 16×16 array was used for functional verification, process corner
analysis, temperature analysis, Monte Carlo simulations, and energy
measurements.

## 2. Design Architecture\*\*

The proposed 10T SRAM cell consists of:

-   \*\*\*\*6T SRAM core:\*\*\*\* Stores the data using cross-coupled
    inverters and access transistors.

-   \*\*\*\*Compute section:\*\*\*\* Four additional transistors
    (N5--N8) provide the read, compute, and CAM functionality.

-   \*\*\*\*Decoupled read ports:\*\*\*\* Separate row-wise and
    column-wise read operations.

-   \*\*\*\*Match line (ML):\*\*\*\* Used for detecting matches and
    mismatches during BCAM searches.

An important feature of this architecture is the self-suppressed
match-line discharge mechanism.

The high-threshold-voltage transistor N8 provides feedback during
match-line discharge. As the match-line voltage decreases, the effective
gate-to-source voltage of N8 also decreases, slowing down the discharge
and preventing the match line from reaching 0 V.

This helps reduce the energy consumed during CAM searches.

## 3. Cadence Virtuoso Project Structure and Schematic Paths\*\*

\*\*\*\*EDA Tool:\*\*\*\* Cadence Virtuoso

\*\*\*\*Simulation Environment:\*\*\*\* Spectre

\*\*\*\*Technology:\*\*\*\* UMC 28 nm PDK (as used in the project setup)

All schematics and simulation testbenches are organized inside the
Cadence Virtuoso library.

### Library Information\*\*

| Parameter \| Details \|

\|---\|---\|

| Library Name \| `project_helper` \|

| Technology \| UMC 28 nm \|

| Design Type \| Transistor-level schematic \|

| Simulator \| Spectre \|

### Schematic Locations\*\*

Update the following paths with the actual library and cell names used
in your Cadence environment.

| Design Block \| Cadence Library \| Cell Name \|

\|---\|---\|---\|

| Single 10T SRAM Cell \| `project_helper` \| `10T_SRAM_Cell` \|

| 2×2 SRAM Array \| `project_helper` \| `10T_SRAM_2_2` \|

| 16×16 SRAM Array \| `project_helper` \| `10T_SRAM_32_32` \|

| driver circuit \| `project_helper` \| `driver` \|

| Sense Amplifier \| `project_helper` \| `sense_amplifier` \|

## 4. Operations Implemented\*\*

The 10T SRAM architecture was verified for five major operations.

### 4.1 Write Operation\*\*

-   Data is written into the selected SRAM row.

-   WWL is activated.

-   WBL and WBLB carry complementary input data.

-   The cross-coupled inverter pair stores the data.

\*\*\*\*Measured Energy:\*\*\*\* 46.6 fJ/bit

### 4.2 Row-wise Read\*\*

-   The selected row is activated using RWL.

-   RBL and RBLB are precharged.

-   The stored data determines the differential bit-line discharge.

-   A differential sense amplifier detects the stored value.

\*\*\*\*Measured Energy:\*\*\*\* 26.52 fJ/bit

### 4.3 Column-wise Read\*\*

-   RWL lines are kept low.

-   RBL and RBLB of the selected column are driven.

-   The HBL voltage depends on the stored data.

-   The output is detected using a reference voltage.

Reference voltage:

VREF = VDD − 100 mV

This operation also enables matrix transposition by writing data
row-wise and reading it column-wise.

\*\*\*\*Measured Energy:\*\*\*\* 7.76 fJ/bit

### 4.4 Binary CAM (BCAM) Search\*\*

-   RBL and RBLB act as search lines (SL and SLB).

-   The match line is precharged high.

-   If the stored data matches the search data, the match line remains
    high.

-   If a mismatch occurs, the match line discharges.

-   The self-suppressed discharge mechanism limits the voltage swing.

\*\*\*\*Measured Energy:\*\*\*\* 0.64 fJ/bit/search

### 4.5 In-Memory Computing (IMC)\*\*

-   Two selected rows are activated simultaneously using their
    respective RWL signals.

-   The bit-line discharge pattern depends on the stored operands.

-   Two single-ended sense amplifiers resolve the computation result.

This allows Boolean operations to be performed directly within the
memory array without transferring the operands to an external processing
unit.

\*\*\*\*Measured Energy:\*\*\*\* 25.92 fJ/bit

## 5. Simulation and Analysis\*\*

The following analyses were performed on the 16×16 SRAM array.

### 5.1 Functional Verification\*\*

All five operations were simulated and verified:

-   Write

-   Row-wise read

-   Column-wise read

-   BCAM search

-   In-memory logic computation

### 5.2 Process Corner Analysis\*\*

The design was simulated under five process corners:

-   TT

-   FF

-   SS

-   FS

-   SF

The match-line delay was measured for each corner.

### 5.3 Temperature Analysis\*\*

The design was evaluated at:

-   -40°C

-   27°C

-   87°C

The effect of temperature on the high-Vth N8 transistor and match-line
voltage was analyzed.

### 5.4 Monte Carlo Analysis\*\*

Monte Carlo simulations were performed to observe the variation in
match-line discharge voltage.

The objective was to evaluate the robustness of the self-suppressed
discharge mechanism against device variations.

### 5.5 Mismatch Analysis\*\*

BCAM search was evaluated for different numbers of mismatching bits:

-   0 mismatches

-   2 mismatches

-   4 mismatches

-   8 mismatches

-   16 mismatches

For the 16-bit row, 16 mismatches represent the all-mismatch worst-case
condition.

### 5.6 Energy Analysis\*\*

Energy consumption was measured for all five operations at the TT
process corner.

## 6. Results and Observations\*\*

### 6.1 Energy per Operation\*\*

| Operation \| Energy \|

\|---\|---:\|

| Write \| 46.6 fJ/bit \|

| Row Read \| 26.52 fJ/bit \|

| Column Read \| 7.76 fJ/bit \|

| In-Memory Logic \| 25.92 fJ/bit \|

| BCAM Search \| 0.64 fJ/bit/search \|

Key observations:

-   Write operation has the highest energy consumption.

-   Column read consumes approximately 3.4 times less energy than row
    read.

-   BCAM search is approximately 40 times more energy-efficient than
    in-memory logic computation.

### 6.2 Match-Line Discharge Analysis\*\*

The measured match-line voltage for different mismatch counts was:

| Number of Mismatches \| ML Voltage \|

\|---:\|---:\|

| 0 \| 930.8 mV \|

| 2 \| 307.5 mV \|

| 4 \| 279.0 mV \|

| 8 \| 252.4 mV \|

| 16 \| 227.0 mV \|

Observations:

-   The match line remains at approximately 0.93 V when all bits match.

-   Increasing the mismatch count reduces the match-line voltage.

-   Even under the all-mismatch condition, the match line does not
    discharge to 0 V.

-   A sensing margin of approximately 623 mV is observed between the
    match and weakest mismatch cases.

This verifies the self-suppressed match-line discharge mechanism.

### 6.3 Process Corner and Temperature Analysis\*\*

The following observations were obtained:

-   The match-line voltage follows the ordering SS \> SF \> TT \> FS \>
    FF at every simulated temperature.

-   Slow NMOS process corners maintain a higher match-line voltage.

-   Match-line voltage decreases by approximately 83--91 mV as
    temperature increases from -40°C to 87°C.

-   The highest observed voltage is approximately 435 mV at SS and
    -40°C.

-   The lowest observed voltage is approximately 218 mV at FF and 87°C.

Only the high-Vth N8 implementation was simulated.

### 6.4 Match-Line Delay\*\*

| Process Corner \| Reference Paper (ns) \| Our Work (ns) \|

\|---\|---:\|---:\|

| FF \| 0.53 \| 0.63 \|

| FS \| 0.58 \| 0.72 \|

| TT \| 0.73 \| 0.89 \|

| SF \| 0.87 \| 0.99 \|

| SS \| 0.91 \| 1.05 \|

Observations:

-   FF has the lowest match-line delay.

-   SS has the highest match-line delay.

-   The same process-corner trend as the reference paper is observed.

-   Our implementation shows approximately 14--24% higher delay across
    the process corners.

### 6.5 Comparison with Reference Paper\*\*

| Metric \| Reference Paper \| Our Work \|

\|---\|---:\|---:\|

| Row Read Energy \| 22.5 fJ/bit \| 26.52 fJ/bit \|

| Column Read Energy \| 19.2 fJ/bit \| 7.76 fJ/bit \|

| Logic Energy \| 29.2 fJ/bit \| 25.92 fJ/bit \|

| BCAM Energy \| 1.05 fJ/bit/search \| 0.64 fJ/bit/search \|

| TT ML Delay \| 0.73 ns \| 0.89 ns \|

| SS ML Delay \| 0.91 ns \| 1.05 ns \|

The implementation demonstrates lower energy consumption for column
read, in-memory logic, and BCAM search compared with the reference
paper.

The match-line delay is higher in our implementation.

Possible reasons discussed include differences in array size, bit-line
and match-line capacitance, PDK, supply voltage, and transistor sizing.
These factors require further investigation.

## 7. Overall Conclusion\*\*

The project successfully demonstrates the functionality of a
reconfigurable 10T SRAM architecture supporting conventional SRAM
operations, column-wise read, BCAM search, and in-memory computing.

The simulations verify the self-suppressed match-line discharge
mechanism across process corners, temperature variations, and Monte
Carlo analysis.

The 16×16 implementation achieves an energy consumption of 0.64
fJ/bit/search for BCAM and 25.92 fJ/bit for in-memory logic computation.

The results demonstrate the potential of reconfigurable SRAM
architectures for energy-efficient memory-centric computing
applications.

## 8. Team Members\*\*

-   Singh Amritkumar

-   Yash Choudhary

\*\*\*\*Institute:\*\*\*\* Indian Institute of Technology Gandhinagar

\*\*\*\*Course:\*\*\*\* VLSI Design

\*\*\*\*Faculty:\*\*\*\* Prof. Joycee M. Mekie

## 9. References\*\*

1\. Z. Zhang et al., "Reconfigurable 10T SRAM for Energy-Efficient CAM
Operation and In-Memory Computing," IEEE Transactions on Very Large
Scale Integration (VLSI) Systems, Vol. 33, No. 4, April 2025.
