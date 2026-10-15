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

# CoreMark Benchmarking on Linux and RISC-V

## 1. Project Overview

This project documents the process of setting up, compiling, running, and validating the EEMBC CoreMark benchmark on Ubuntu Linux.

The long-term objective is to execute CoreMark on the PicoRV32 RISC-V processor through RTL simulation and study its performance.

The first milestone was to successfully run and validate CoreMark on the native Linux host.

### Project Goals

- Understand the CoreMark benchmarking workflow.
- Set up the official CoreMark source code.
- Configure the build environment.
- Compile and execute the benchmark.
- Resolve build and linking issues.
- Validate the benchmark output.
- Record execution time and iterations per second.
- Prepare for future PicoRV32 benchmarking.

---

## 2. What Is CoreMark?

CoreMark is a processor benchmark developed by EEMBC to measure the performance of CPU cores.

It executes several computational workloads, including:

- Linked-list processing
- Matrix operations
- State-machine processing
- CRC calculations

These workloads help evaluate different aspects of processor performance.

CoreMark is useful when comparing processor implementations, provided that the compiler, compiler flags, benchmark configuration, and execution environment are reported correctly.

### Important Metric

CoreMark reports the number of benchmark iterations completed per second.

A higher iterations-per-second value indicates higher throughput for the particular benchmark configuration.

However, results should only be compared when the execution environments and benchmark configurations are comparable.

---

## 3. Development Environment

### Hardware and Software

| Component | Details |
|---|---|
| Operating System | Ubuntu Linux |
| Benchmark | EEMBC CoreMark |
| Compiler | GCC 15.2.0 for the successful host run |
| Compiler Optimization | `-O2` |
| Benchmark Configuration | `PERFORMANCE_RUN` |
| Final Iterations | 500,000 |
| Host Execution Type | Native Linux executable |
| Future Target | PicoRV32 RISC-V processor |

### Directories Used

CoreMark source directory:

```text
~/coremark-benchmark
```

Important files and directories:

```text
coremark-benchmark/
├── Makefile
├── README.md
├── LICENSE.md
├── core_main.c
├── core_list_join.c
├── core_matrix.c
├── core_state.c
├── core_util.c
├── coremark.h
├── barebones/
├── linux/
├── posix/
└── results/
```

The `results/` directory is used to store benchmark logs and reports.

---

## 4. Downloading CoreMark

The official CoreMark source code was cloned from EEMBC's GitHub repository.

Command:

```bash
mkdir -p ~/coremark-benchmark
cd ~/coremark-benchmark
git clone https://github.com/eembc/coremark.git .
```

The source directory was inspected using:

```bash
ls
```

The repository contained the CoreMark source files, Makefile, platform-specific configurations, and documentation.

Official source repository:

https://github.com/eembc/coremark

---

## 5. Understanding the CoreMark Source Files

### `core_main.c`

Contains the main benchmark execution logic.

It coordinates the benchmark workload and performs the required initialization and validation.

### `core_list_join.c`

Contains linked-list processing operations used by the benchmark.

These operations exercise pointer manipulation and data-structure processing.

### `core_matrix.c`

Contains matrix-related computational workloads.

These operations exercise arithmetic and data-processing operations.

### `core_state.c`

Contains state-machine processing workloads.

These operations exercise state transitions and associated computations.

### `core_util.c`

Contains utility functions used by the benchmark.

### `coremark.h`

Contains common definitions, data structures, and declarations used by the benchmark source files.

### `core_portme.h` and `core_portme.c`

These files are part of the platform-porting layer.

They provide platform-dependent definitions and functions needed to run CoreMark on a particular environment.

The platform port is important because timing, input/output, and other system-dependent operations may differ between a native Linux system and a simulated processor.

---

## 6. Initial RISC-V Cross-Compilation Attempt

The initial objective was to prepare CoreMark for a 32-bit RISC-V environment.

The installed cross-compiler was:

```text
riscv64-unknown-elf-gcc
```

Its target architecture was checked with:

```bash
riscv64-unknown-elf-gcc -dumpmachine
```

The output was:

```text
riscv64-unknown-elf
```

The intended compilation settings included:

```text
-march=rv32im
-mabi=ilp32
-O2
```

These settings request RV32IM code generation, the ILP32 ABI, and optimization.

### Initial Compilation Command

```bash
cd ~/coremark-benchmark

make -B PORT_DIR=barebones \
ITERATIONS=10 \
CC=riscv64-unknown-elf-gcc \
XCFLAGS="-march=rv32im -mabi=ilp32 -O2" \
compile
```

The `-B` option forces Make to rebuild targets.

### Compilation Result

The CoreMark source files compiled into object files, including:

```text
core_list_join.o
core_main.o
core_matrix.o
core_state.o
core_util.o
```

The object files were verified with:

```bash
ls -lh core*.o
```

The compilation stage was successful after addressing a missing `size_t` declaration in the barebones port header.

The following include was inserted before the `size_t` typedef:

```c
#include <stddef.h>
```

This header provides the definition of `size_t`.

**Important:** Successful compilation of object files does not mean that the complete benchmark has been linked or executed.

---

## 7. Initial Linking Failure

The next step attempted to link the RISC-V object files into an executable.

The first linking attempt failed because the barebones configuration specified:

```makefile
LD = gld
```

The linker command could not find `gld`.

### Linker Configuration Change

The linker setting was changed using:

```bash
cd ~/coremark-benchmark

sed -i 's/^LD[[:space:]]*=.*/LD = $(CC)/' barebones/core_portme.mak
```

This changed the linker driver setting to use the configured compiler.

### Second Linking Failure

The subsequent linking attempt reported missing components:

```text
cannot find crt0.o
cannot find -lc
cannot find -lgloss
```

These errors indicated that the required startup code and libraries were unavailable for that build configuration.

The cross-compiled CoreMark executable was therefore not successfully produced through this barebones build attempt.

**Conclusion:** This cross-compilation attempt did not produce a valid PicoRV32 benchmark result. A suitable runtime, linker configuration, and processor-simulation integration are still required.

---

## 8. Switching to the Linux Host Port

To get an actual CoreMark execution and verify the benchmark workflow, the Linux port was used.

The Linux port runs the benchmark as a native Linux executable.

This is different from running a cross-compiled program on a simulated RISC-V processor.

### Initial Host Test

The benchmark was built and executed using:

```bash
cd ~/coremark-benchmark

make clean

make PORT_DIR=linux ITERATIONS=10 CC=gcc
```

The execution generated log files, including:

```text
run1.log
run2.log
```

The logs were inspected using:

```bash
cat run1.log
```

and:

```bash
cat run2.log
```

### Initial Result

The initial test reported:

```text
Total ticks      : 0
Total time (secs): 0.000000
Iterations       : 10
```

The benchmark also reported:

```text
ERROR! Must execute for at least 10 secs for a valid result!
```

This meant that the run was too short to produce a valid timed result.

---

## 9. Increasing the Iteration Count

The number of benchmark iterations was increased to obtain a sufficiently long run.

### Test with 10,000 Iterations

Command:

```bash
cd ~/coremark-benchmark

make clean

make PORT_DIR=linux ITERATIONS=10000 CC=gcc
```

The result reported:

```text
Total ticks      : 1033
Total time (secs): 1.033000
Iterations/Sec   : 9680.542110
Iterations       : 10000
```

The benchmark still failed the minimum execution-time requirement.

### Test with 200,000 Iterations

Command:

```bash
cd ~/coremark-benchmark

make clean

make PORT_DIR=linux ITERATIONS=200000 CC=gcc
```

The result reported:

```text
Total ticks      : 5195
Total time (secs): 5.195000
Iterations/Sec   : 38498.556304
Iterations       : 200000
```

The benchmark again reported that execution must last at least 10 seconds.

### Final Test with 500,000 Iterations

The iteration count was increased to 500,000.

Command:

```bash
cd ~/coremark-benchmark

make clean

make PORT_DIR=linux ITERATIONS=500000 CC=gcc
```

This produced a run longer than 10 seconds and passed the benchmark's correctness validation.

---

## 10. Final Successful Host Benchmark

### Recorded Output

```text
2K performance run parameters for coremark.
CoreMark Size    : 666
Total ticks      : 13531
Total time (secs): 13.531000
Iterations/Sec   : 36952.183874
Iterations       : 500000
Compiler version : GCC15.2.0
Compiler flags   : -O2 -DPERFORMANCE_RUN=1  -lrt
Memory location  : Please put data memory location here
seedcrc          : 0xe9f5
[0]crclist       : 0xe714
[0]crcmatrix     : 0x1fd7
[0]crcstate      : 0x8e3a
[0]crcfinal      : 0xbd59
Correct operation validated. See README.md for run and reporting rules.
CoreMark 1.0 : 36952.183874 / GCC15.2.0
```

### Final Result Summary

| Metric | Result |
|---|---:|
| Benchmark | CoreMark 1.0 |
| CoreMark Size | 666 |
| Iterations | 500,000 |
| Total Ticks | 13,531 |
| Total Execution Time | 13.531 seconds |
| Iterations per Second | 36,952.183874 |
| Compiler | GCC 15.2.0 |
| Optimization | `-O2` |
| Run Configuration | `PERFORMANCE_RUN=1` |
| Correctness Validation | Passed |
| Execution Environment | Native Linux host |

### Final Status

**PASS — CoreMark host execution and correctness validation completed.**

The recorded result is:

```text
CoreMark 1.0 : 36952.183874 / GCC15.2.0
```

This is a host-specific result and must not be presented as a PicoRV32 performance score.

---

## 11. Saving the Benchmark Results

The benchmark results can be stored in a dedicated directory.

Create the directory:

```bash
cd ~/coremark-benchmark

mkdir -p results
```

Copy the successful benchmark log:

```bash
cp run1.log results/coremark-linux-host.log
```

Create a Markdown report:

```bash
nano results/coremark-linux-host.md
```

Suggested report:

```markdown
# CoreMark Linux Host Benchmark

## Status

PASS — correctness validation completed.

## Configuration

- Benchmark: CoreMark 1.0
- CoreMark Size: 666
- Iterations: 500000
- Compiler: GCC 15.2.0
- Compiler flags: -O2 -DPERFORMANCE_RUN=1
- Platform: Ubuntu Linux host

## Results

- Total ticks: 13531
- Total time: 13.531 seconds
- Iterations per second: 36952.183874

## Validation

The benchmark reported:

Correct operation validated.

## Scope

This is a native Linux host result.

It is not a PicoRV32 RTL simulation result.

A separate RISC-V build and PicoRV32 simulation are required to
measure benchmark performance on the processor.
```

Save the file in Nano using:

1. `Ctrl + O`
2. Press `Enter`
3. `Ctrl + X`

---

## 12. Understanding the Result

### What Does Iterations per Second Mean?

The reported metric is:

```text
Iterations/Sec = 36952.183874
```

It represents the number of CoreMark iterations completed per second on the tested host under the recorded configuration.

It is a throughput metric.

It should not be interpreted as the number of CPU instructions executed per second.

### Why Was the First Result Invalid?

The first runs were too short.

CoreMark requires a sufficiently long execution interval for a valid timed result.

Increasing the iteration count increased the total execution time until the benchmark passed this requirement.

### What Does "Correct Operation Validated" Mean?

CoreMark checks the correctness of its workload using expected results and CRC checks.

The final run reported:

```text
Correct operation validated.
```

This indicates that the benchmark's correctness checks passed for that execution.

### Why Must the Platform Be Reported?

A native Linux executable uses the host computer's processor and operating system.

A PicoRV32 simulation executes instructions through a simulated RISC-V core.

The two environments have different execution characteristics and cannot be treated as equivalent.

---

## 13. Host Benchmark vs. PicoRV32 Benchmark

| Property | Linux Host Test | PicoRV32 Simulation |
|---|---|---|
| Execution environment | Native Linux | RTL processor simulation |
| Compiler target | Host architecture | RISC-V architecture |
| CPU executing the program | Host CPU | Simulated PicoRV32 |
| Timing source | Host timing facilities | Simulation cycles or a defined processor timer |
| Result obtained | 36,952.183874 iterations/second | Not yet measured |
| Correctness validation | Passed | Pending |
| Suitable for reporting as a PicoRV32 score? | No | Yes, after successful execution and validation |

A meaningful PicoRV32 benchmark requires a suitable startup/runtime environment, memory configuration, program-loading procedure, and simulation testbench.

The benchmark must execute on the simulated processor rather than merely compile into a RISC-V binary.

---

## 14. Planned PicoRV32 Benchmarking Workflow

The next phase is to run CoreMark on PicoRV32.

The planned workflow is:

1. Configure CoreMark for a freestanding RISC-V environment.
2. Provide the required startup code and linker script.
3. Implement or adapt the CoreMark platform functions.
4. Configure the memory layout.
5. Compile CoreMark for the supported PicoRV32 instruction set.
6. Convert the executable into a format supported by the simulation.
7. Load the program into PicoRV32's memory.
8. Run the RTL simulation.
9. Capture the benchmark output and execution status.
10. Measure simulation cycles using a defined method.
11. Verify CoreMark correctness.
12. Record the compiler configuration and simulation settings.
13. Document the results separately from the Linux host results.

The exact implementation depends on the PicoRV32 testbench and runtime already available in the development environment.

---

## 15. Important Benchmarking Considerations

For reproducible results, document:

- Processor or host model
- Compiler name and version
- Compiler optimization flags
- Benchmark version
- Iteration count
- Timing method
- Memory configuration
- Execution environment
- Correctness-validation status

Do not compare results without checking whether the benchmark configuration and execution environments are comparable.

A native host result is useful for verifying the benchmark workflow, but it does not establish the performance of a RISC-V processor.

---

## 16. References

### Official CoreMark Repository

https://github.com/eembc/coremark

### PicoRV32 Repository

https://github.com/YosysHQ/picorv32

### CoreMark Documentation

Refer to the official CoreMark README and platform-porting documentation for benchmark execution and reporting requirements.

---

## 17. Project Status

### Completed

- [x] Cloned the official CoreMark repository.
- [x] Inspected the benchmark source files.
- [x] Attempted RISC-V cross-compilation.
- [x] Identified the barebones linker configuration issue.
- [x] Identified missing startup and library dependencies.
- [x] Switched to the Linux host port.
- [x] Executed the benchmark on the host.
- [x] Increased the iteration count.
- [x] Obtained an execution longer than 10 seconds.
- [x] Passed CoreMark correctness validation.
- [x] Recorded the host benchmark result.

### Pending

- [ ] Complete the freestanding RISC-V build.
- [ ] Integrate CoreMark with the PicoRV32 simulation.
- [ ] Execute the benchmark on the simulated processor.
- [ ] Capture and validate PicoRV32 output.
- [ ] Measure simulation cycles.
- [ ] Publish a separate PicoRV32 benchmark report.

---

## Conclusion

This project successfully completed a CoreMark 1.0 host benchmark on Ubuntu Linux.

The final recorded result was:

**36,952.183874 iterations per second**

The benchmark ran for 13.531 seconds and reported successful correctness validation.

The next milestone is to integrate CoreMark with PicoRV32 and measure performance in RTL simulation. That result will be documented separately to distinguish native host performance from simulated RISC-V processor performance.








