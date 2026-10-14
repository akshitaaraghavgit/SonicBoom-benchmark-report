# Test 01 — PicoRV32 Regression Simulation

## 1. Objective

The objective of this test was to build and execute the PicoRV32 processor's testbench in an Ubuntu environment using a RISC-V cross-compiler.

This was an initial simulation test to verify that the existing test setup could execute successfully before proceeding to dedicated performance benchmarking.

## 2. Test Environment

| Component | Configuration |
|---|---|
| Operating System | Ubuntu 26.04.1 LTS |
| Processor under test | PicoRV32 |
| Hardware Description Language | Verilog |
| Simulator | Icarus Verilog / vvp |
| Cross-compiler | `riscv64-unknown-elf-gcc` |
| Target architecture | RV32 |
| Test type | Regression simulation |

## 3. Command Executed

The following command was used to build and run the test target:

```bash
cd ~/picorv32
make test TOOLCHAIN_PREFIX=/usr/bin/riscv64-unknown-elf-
```

![Dhrystone timing results](images/dhrystone-timing-results.png)

### Explanation

- `cd ~/picorv32` navigates to the PicoRV32 source directory.
- `make test` invokes the project's `test` target.
- `TOOLCHAIN_PREFIX` specifies the installed RISC-V toolchain prefix.
- The cross-compiler generates code for the RISC-V target instead of the host computer's native architecture.

## 4. Test Execution Flow

The test follows the general workflow below:

1. The build system processes the test target and its dependencies.
2. The RISC-V cross-compiler compiles the required firmware.
3. The firmware is prepared for execution by the simulated processor.
4. The Verilog testbench simulates the processor's behaviour.
5. The testbench reports the test outcome and terminates the simulation.

**Note:** This describes the general workflow. The exact firmware components, checks, and termination mechanism depend on the project's testbench and Makefile implementation.

## 5. Observed Output

The simulation produced the following output:

```text
Number of fast external IRQs counted: 54
Number of slow external IRQs counted: 6
Number of timer IRQs counted: 22
TRAP after 477707 clock cycles
ALL TESTS PASSED.
testbench.v:266: $finish called at 4778170000 (1ps)
```

## 6. Results and Interpretation

### 6.1 Test Status

The testbench reported:

```text
ALL TESTS PASSED.
```

This indicates that the checks performed by this test run passed according to the testbench.

It does not establish that every instruction, interrupt scenario, or possible processor configuration has been verified.

### 6.2 Interrupt Counts

The simulation reported the following interrupt counts:

| Interrupt Category | Reported Count |
|---|---:|
| Fast external IRQs | 54 |
| Slow external IRQs | 6 |
| Timer IRQs | 22 |

IRQ stands for Interrupt Request.

Interrupts allow events to request the processor's attention. The precise meaning of these reported counts depends on the counter and testbench implementation and should be confirmed by inspecting the relevant source code.

### 6.3 Reported Clock Cycles

The test reported:

```text
TRAP after 477707 clock cycles
```

The reported count is 477,707 clock cycles before the trap event.

A clock cycle is one cycle of the processor's clock signal. Instructions do not necessarily take exactly one clock cycle each.

The cycle count alone does not establish execution time in seconds, instructions per cycle (IPC), or a standard benchmark score.

The testbench and firmware must be inspected to determine precisely what interval this count represents.

### 6.4 Simulation Termination

The simulator reported:

```text
testbench.v:266: $finish called at 4778170000 (1ps)
```

The `$finish` system task terminates the Verilog simulation.

The reported timestamp is simulation time, expressed using the simulator's configured time units and precision. It should not be confused with the clock-cycle count.

## 7. What This Test Establishes

Based on the observed output, this run establishes that:

- The `make test` command completed far enough to run the simulation.
- The testbench reported `ALL TESTS PASSED`.
- Interrupt counts were reported.
- The simulation reached its `$finish` statement.

This is a successful test run for the tested configuration.

## 8. What This Test Does Not Establish

This test does not, by itself, establish:

- Complete verification of the PicoRV32 processor.
- A CoreMark or Dhrystone benchmark score.
- Execution time in seconds.
- Instructions per cycle (IPC).
- Maximum clock frequency.
- FPGA resource utilization.
- Power consumption.

These require additional measurements, test configurations, or tools.

## 9. Functional Testing vs Performance Benchmarking

Functional testing checks whether a design behaves as expected for the scenarios being tested.

Performance benchmarking measures how efficiently a system completes a defined workload using metrics such as execution cycles, execution time, or benchmark-specific scores.

This experiment is an initial regression simulation. It should not be presented as a completed performance benchmark.

## 10. Next Steps

1. Inspect the testbench to understand its checks and termination mechanism.
2. Determine how the reported clock-cycle count is generated.
3. Identify a repeatable benchmark workload.
4. Establish a reliable method for collecting performance metrics.
5. Record benchmark configurations and measured results.
6. Analyse the results and document any limitations.

## Conclusion

The initial PicoRV32 regression simulation ran successfully and reported `ALL TESTS PASSED`.

This provides a starting point for further testing and performance analysis. Dedicated benchmark workloads and verified measurement methods are still required before drawing conclusions about processor performance.

![PicoRV32 Test Execution](images/Screenshot%20From%202026-10-14%2007-37-15.png)

![PicoRV32 Test Results](images/Screenshot%20From%202026-10-14%2007-37-29.png)

# Test 02: Dhrystone Instruction Timing Analysis

## Objective

Analyze instruction-level timing information produced while
running the Dhrystone workload on the simulated PicoRV32 processor.

## Environment

- Operating system: Ubuntu
- Processor under test: PicoRV32
- Simulation tool: Icarus Verilog (`iverilog` and `vvp`)
- Compiler: RISC-V GCC
- Workload: Dhrystone
- Timing output: `dhrystone/timing.txt`

## Command

```bash
cd ~/picorv32/dhrystone
make timing USE_MYSTDLIB=1 TOOLCHAIN_PREFIX=/usr/bin/riscv64-unknown-elf-
```
![Dhrystone Timing Results](images/Screenshot%20From%202026-10-14%2009-21-29.png)


---

## Test 03: Dhrystone Baseline Performance Benchmark

### 1. Objective

The objective of this test was to execute the Dhrystone benchmark on the PicoRV32 processor simulation and collect baseline performance metrics.

Unlike Test 02, which focused on instruction-level timing analysis, this test focuses on the overall benchmark execution and its reported performance metrics.

The experiment helps us understand how many instructions are executed, how many simulated clock cycles are consumed, and how efficiently the processor executes the workload.

### 2. Environment

| Component | Details |
|---|---|
| Processor | PicoRV32 |
| Benchmark | Dhrystone |
| Execution environment | Ubuntu Linux |
| Simulation tool | Icarus Verilog (`iverilog` and `vvp`) |
| Compiler | RISC-V GCC |
| Instruction set | RV32IM |
| Optimization | `-O3` |
| Execution type | RTL simulation |

### 3. Commands Used

First, navigate to the Dhrystone directory:

```bash
cd ~/picorv32/dhrystone
```

Run the benchmark using the custom standard library:

```bash
make test USE_MYSTDLIB=1 TOOLCHAIN_PREFIX=/usr/bin/riscv64-unknown-elf- 2>&1 | tee /tmp/dhrystone-run.log
```

This command builds and runs the Dhrystone testbench. The output is displayed in the terminal and saved to `/tmp/dhrystone-run.log` for later inspection.

To extract the important results:

```bash
grep -E 'Number_Of_Runs|User_Time|User_Insn|Cycles_Per_Instruction|Dhrystones_Per_Second_Per_MHz|DMIPS_Per_MHz|Execution ends|TRAP|should be:' /tmp/dhrystone-run.log
```

This filters the log to show the benchmark's reported metrics and selected functional-check messages.

### 4. Understanding the Build and Execution Flow

The benchmark execution follows this sequence:

1. **C source code:** Dhrystone is implemented in C across files such as `dhry_1.c` and `dhry_2.c`.
2. **Compilation:** RISC-V GCC compiles the source code for the RV32IM instruction set.
3. **Linking:** The compiled object files are combined to produce the executable used by the testbench.
4. **Hex generation:** The program is converted into a memory image that the simulation can load.
5. **RTL simulation:** Icarus Verilog runs the PicoRV32 testbench, which executes the compiled program.
6. **Metric collection:** The benchmark reports its run count, instruction count, cycle count, CPI, and normalized performance metrics.
7. **Termination:** The testbench terminates after the benchmark finishes.

The `USE_MYSTDLIB=1` option selects the project's custom library configuration. This was necessary because the default build initially failed due to a missing `stdio.h` header in the available toolchain environment.

The `TOOLCHAIN_PREFIX` option overrides the compiler prefix configured in the Makefile so that the installed RISC-V compiler can be used.

### 5. Actual Benchmark Results

The simulation reported the following results:

| Metric | Result |
|---|---:|
| Number of Runs | 100 |
| Simulated Clock Cycles (`User_Time`) | 139,391 |
| Instructions Executed (`User_Insn`) | 35,725 |
| Cycles Per Instruction (CPI) | 3.901 |
| Dhrystones Per Second Per MHz | 717 |
| DMIPS Per MHz | 0.408 |

These are the results reported by the benchmark under the current simulation and configuration.

### 6. Explanation of the Metrics

#### A. Number of Runs

```text
Number_Of_Runs: 100
```

The benchmark completed 100 Dhrystone iterations/runs according to its configured run count.

A run count is the workload size used by the benchmark. It is not a performance score by itself.

#### B. User_Time

```text
User_Time: 139391 cycles
```

This is the cycle count reported by the benchmark's timing mechanism.

It represents the simulated processor cycles counted by the current implementation. It should not automatically be interpreted as elapsed wall-clock time or as a physical FPGA measurement.

#### C. User_Insn

```text
User_Insn: 35725 insn
```

The benchmark reported 35,725 executed instructions.

This value represents the instruction count reported by the benchmark's instrumentation. Its exact scope depends on how the counter is implemented in this test setup.

#### D. Cycles Per Instruction (CPI)

CPI measures the average number of clock cycles required per executed instruction.

Formula:

```text
CPI = Total Clock Cycles / Total Instructions
```

Using the reported values:

```text
CPI = 139391 / 35725
CPI ≈ 3.901
```

The calculated value agrees with the reported CPI.

A lower CPI generally indicates fewer cycles per instruction for the measured workload. However, CPI alone does not establish which processor is faster because clock frequency, instruction count, workload, and measurement methodology also matter.

#### E. Dhrystones Per Second Per MHz

```text
Dhrystones_Per_Second_Per_MHz: 717
```

This is the normalized Dhrystone performance value reported by the benchmark.

It expresses the reported Dhrystone throughput normalized by clock frequency in MHz, according to the benchmark's timing and calculation logic.

The value should be compared with other results only when the benchmark version, compiler options, workload, counter implementation, and measurement method are sufficiently consistent.

#### F. DMIPS Per MHz

```text
DMIPS_Per_MHz: 0.408
```

DMIPS means *Dhrystone Millions of Instructions Per Second*.

The benchmark uses a reference value of 1757 Dhrystones per second to calculate its normalized DMIPS result.

The calculation is:

```text
DMIPS/MHz = Dhrystones per Second per MHz / 1757
```

Using the reported value:

```text
DMIPS/MHz = 717 / 1757
DMIPS/MHz ≈ 0.408
```

This agrees with the reported result.

DMIPS/MHz is a benchmark-specific normalized metric. It should not be treated as a universal measure of processor performance.

### 7. Functional Output Verification

The benchmark also prints expected and actual values for selected variables.

Examples from the output include:

```text
Int_Glob: 5
Bool_Glob: 1
Ch_1_Glob: A
Ch_2_Glob: B
Arr_1_Glob[8]: 7
Arr_2_Glob[8][7]: 110
```

The benchmark compares these values with expected results to check whether its workload is producing the expected program state.

For example:

```text
Number_Of_Runs = 100
Expected Arr_2_Glob[8][7] = Number_Of_Runs + 10
                            = 100 + 10
                            = 110
```

The observed value of 110 matches this expected value.

These checks provide evidence of functional execution, while the reported metrics provide information about the measured workload.

### 8. Why This Test Matters

This experiment establishes a baseline for measuring PicoRV32 performance using a software benchmark.

It demonstrates how to:

- Build a C benchmark for a RISC-V target.
- Execute the compiled program in an RTL simulation.
- Inspect instruction and cycle counts reported by the benchmark.
- Calculate and interpret CPI.
- Understand normalized Dhrystone and DMIPS/MHz metrics.
- Record reproducible experimental results for future comparisons.

The same general workflow can be used when evaluating other processors or benchmark programs, provided the build and measurement configurations are adapted appropriately.

### 9. Limitations and Important Notes

1. **Simulation, not physical hardware:** These results were obtained from an RTL simulation, not from running the benchmark on an FPGA board.

2. **Counter implementation matters:** The interpretation of `User_Time` and `User_Insn` depends on the benchmark's counter implementation. These values should be inspected before using them for rigorous processor comparisons.

3. **Compiler configuration matters:** Compiler version, optimization level, ABI, instruction set, and library configuration can affect benchmark behavior and results.

4. **Dhrystone is a synthetic benchmark:** It represents a particular style of integer workload and does not represent every real-world application.

5. **No universal performance ranking:** The reported DMIPS/MHz value should not be used alone to conclude that one processor is faster than another.

6. **Termination output:** The log ends with `TRAP`. In this simulation, termination output must be interpreted in the context of the testbench and its exit mechanism; the word alone does not establish that the benchmark failed.

### 10. Conclusion

The Dhrystone benchmark was executed in the PicoRV32 RTL simulation with 100 runs.

The benchmark reported 139,391 cycles, 35,725 instructions, a CPI of 3.901, 717 Dhrystones per second per MHz, and 0.408 DMIPS/MHz.

The CPI and DMIPS/MHz calculations agree with the reported values. The experiment establishes an initial performance baseline and demonstrates the workflow for compiling, simulating, and evaluating a RISC-V software benchmark.

Further work should validate the counter implementation and measurement configuration before using these results for rigorous comparisons with other processor cores.

### 11. Evidence
[Dhrystone Instruction Timing Results](images/Screenshot%20From%202026-10-14%2009-21-29.png)
[Dhrystone Instruction Timing Results](images/Screenshot%20From%202026-10-14%2009-21-29.png)
[Dhrystone Performance Results](images/Screenshot%20From%202026-10-14%2009-58-45.png)








