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









