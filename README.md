# DSP Subsystem: Formal Verification and Equivalence Check Automation using Synopsys Toolchain

A comprehensive automation flow for performing formal verification and logic equivalence checks on a DSP subsystem, ensuring RTL-Gate netlist functional parity using Synopsys Formality and VC Formal.

## Table of Contents
- Tools Used
- Project Structure
- RTL Design Overview
- Equivalence and Formal Flow
- TCL Scripts
- Testbench
- Folder Tree

## Tools Used

| Step             | Tool          |
|------------------|---------------|
| Formal Check     | VC Formal     |
| Equivalence Check| Formality     |
| Simulation       | VCS           |
| Debug            | Verdi         |

## Project Structure

```
dsp_formal_equiv_flow/
├── README.md
├── doc/
│   ├── dsp_block_diagram.png
│   └── formal_verification_summary.md
├── rtl/
│   ├── dsp_top.v
│   ├── fir_filter.v
│   └── control_logic.v
├── scripts/
│   ├── formal/vc_formal_check.tcl
│   ├── formal/formality_setup.tcl
│   └── sim/vcs_tb_compile.do
├── netlist/
│   └── dsp_top_gate.v
├── constraints/
│   └── dsp_top.sdc
├── reports/
│   ├── formality/
│   └── vcformal/
└── testbench/
    └── dsp_tb.v
```

## RTL Design Overview

```verilog
module dsp_top (
    input clk,
    input rst,
    input [15:0] din,
    output [15:0] dout
);
    wire [15:0] fir_out;

    fir_filter u_fir (
        .clk(clk),
        .rst(rst),
        .din(din),
        .dout(fir_out)
    );

    control_logic u_ctrl (
        .clk(clk),
        .rst(rst),
        .data_in(fir_out),
        .data_out(dout)
    );
endmodule
```

## Formal and Equivalence Flow

1. **VC Formal Assertion Check**:
   ```bash
   vcformal -f scripts/formal/vc_formal_check.tcl
   ```
2. **Formality Equivalence**:
   ```bash
   formality -f scripts/formal/formality_setup.tcl
   ```
3. **VCS Simulation**:
   ```bash
   vcs -f scripts/sim/vcs_tb_compile.do
   ```

## TCL Scripts

**vc_formal_check.tcl**:
```tcl
analyze -sv12 ../rtl/*.v
elaborate dsp_top
set_top dsp_top
read_sva ../rtl/*.sv
run_checks -type lint
prove -all
report_proved > ../reports/vcformal/formal_proof_results.rpt
```

**formality_setup.tcl**:
```tcl
read_db -rtl ../rtl/*.v
read_db -gate ../netlist/dsp_top_gate.v
read_sdc ../constraints/dsp_top.sdc
set_top dsp_top
match
verify
report_verification > ../reports/formality/equiv_result.rpt
```

## Testbench

```verilog
module dsp_tb;
    reg clk;
    reg rst;
    reg [15:0] din;
    wire [15:0] dout;

    dsp_top dut (
        .clk(clk),
        .rst(rst),
        .din(din),
        .dout(dout)
    );

    initial clk = 0;
    always #5 clk = ~clk;

    initial begin
        rst = 1; din = 16'd0;
        #10 rst = 0;
        repeat (50) begin
            din = $random;
            #10;
        end
        $finish;
    end
endmodule
```

## Folder Tree

```
dsp_formal_equiv_flow/
├── constraints/           # Timing constraints
├── doc/                   # Documentation and diagrams
├── netlist/               # Gate-level netlist
├── reports/               # Tool reports
├── rtl/                   # RTL source files
├── scripts/               # Automation scripts
├── testbench/             # Simulation testbenches
└── README.md
```

---

## Author
Adarsh Prakash  
Email: kumaradarsh663@gmail.com  
LinkedIn: [https://linkedin.com/in/adarshprakash](https://linkedin.com/in/adarshprakash)

