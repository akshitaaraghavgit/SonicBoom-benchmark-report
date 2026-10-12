## Benchmark Comparison

| Benchmark / Metric | Type | Primary Focus / Workload | Output Metric | Purpose in RISC-V BOOM Evaluation |
| :--- | :--- | :--- | :--- | :--- |
| **CoreMark** | Software Suite | Matrix manipulation, pointer chasing, state machines, and heavy integer math | Iterations / Sec (and CoreMark/MHz) | Evaluates overall CPU performance, memory throughput, and pipeline efficiency under heavier loads. |
| **Dhrystone** | Synthetic Benchmark | String handling, logic loops, and basic integer operations | DMIPS (Dhrystone MIPS) or DMIPS/MHz | Acts as a fast sanity check to verify core execution flow and basic integer instruction latency. |
| **Embench-IoT** | Modern Benchmark Suite | Diverse real-world embedded workloads (CRC32, sorting, math, finite state machines) | Geometric mean score / execution time | Measures generalizability and performance across a wide variety of practical IoT algorithms rather than a single loop. |
| **Fmax** | Hardware Metric (Timing Analysis) | Maximum achievable clock frequency based on critical path delay | MHz (Megahertz) | Determines the physical limits of FPGA synthesis and hardware timing closure for the BOOM core variant. |
