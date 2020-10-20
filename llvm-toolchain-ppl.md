# OpenHW Preliminary Project Proposal: LLVM/Sail SW development tool chain for CORE-V

## Summary of project

This proposal is for a baseline development tool chain for CORE-V based on up-to-date compiler and simulation techology, comprising:

- Clang/LLVM tool chain for C and C++:
  - compiler
  - assembler
  - linker
  - emulation library (`compiler-rt`)

- CORE-V support in the [RISC-V model for Sail](https://github.com/rems-project/sail-riscv), an instruction set simulator with formal verification capabilities

The CORE-V LLVM tool chain may rely initially on the GNU CORE-V binutils developed as part of the GNU CORE-V tool chain:

  - assembler (`gas`)
  - linker (GNU `ld`)
  - low level utilities for manipulating object files.

The tool chain will interface to the following other components of the CORE-V development ecosystem
- GNU debugger (GDB)
- standard C libraries (`newlib`, GNU `_GlibC_`)
- GNU C++ standard library (`libstdc++v3`)

[Sail](https://github.com/rems-project/sail) is an instruction set simulator written in OCaml.
Thanks to the strictly defined semantics of OCaml it is possible to apply formal verification to processors models described in that language.
Since a preliminary RISC-V model for Sail already exists, the aim is to provide a model of the CVA6 core which will capture the behavior of all operation modes and programmer-visible elements of the core (e.g., all CVA6-specific CSRs).

Because of the scale of the project and the current maturity level of LLVM for RISC-V in general, the LLVM-related work is decomposed into three phases:

<!--- 0. resolution of known LLVM issues observed on the CVA6 verification flow;
  --->
1. Support for bare metal use of C with CVA6 (64-/32-bit) together with the _Newlib_ C library;
2. Support for Linux application use of C and C++ with CVA6 (32-/64-bit) together with the _GlibC_ C library; and
3. Support for other RTOSes.

The proposal addresses just the first phase. Other phases will be the subject of separate proposals. Phase 1 will provide the following:

- suitability of Clang/LLVM for HW verification of CVA6:
  - seamless support of multiple datapath widths (multi-XLEN support)
  - resolution of functional failures identified on the current CVA6 verification flow

- support of differentiating characteristics of the CVA6 cores:
  - pipeline depth
  - specific CSRs

- support for a generic CORE-V instruction set extension interface:
  - in the Clang/LLVM tool chain
  - in the Sail simulator
  - NOTE: this will provide a commercial driver for future tool chain development for new extensions, whether public or proprietary.

In order to support the Software TG's primary goal of developing a thriving commercial ecosystem, only a basic implementation will be provided.
By basic implementation we mean that:

- the assembler/linker will support the instructions;
- the compiler will have intrinsic/builtin function support;
- the compiler will have patterns to generate the instructions from C code in obvious circumstances.

There will be no attempt to provide compiler optimizations.  The development of optimizations will be under the responsibility of:

- software companies which can rely on the amount of work still needing to be done to drive their businesses; and of
- HW and system integration companies which can use specific optimizations targeted at their products as market differentiators.

In order to support the Software TG secondary goal of upstreaming all open source tool developments:

- all Clang/LLVM development will be kept compliant with the LLVM Foundation coding and quality assurance standards;
- all Sail development will be aligned on REMS project standards/practices;
- the implemenation will follow the upstream tool design and coding conventions; and
- any vendor-specific modifications or additions will be duly isolated into extensions, so as to maintain a fully functional common open source code base.

The finished work will be contributed to the LLVM community and maintained upstream.

### Nature of the development

This project requires the modification of a set of existing public open source code bases maintained as projects of the LLVM Foundation.  Therefore, the processes used within OpenHW will reflect the processes of those upstream projects.

These code bases are of substantial size:

- Clang: 1.75 MLOC C++
- LLVM: 2.1 MLOC C++

The code bases include substantial regression test suites (1.5 MLOC including `libcxx` C++ library tests), and success with these test suites is a pre-requisite of upstream acceptance of any patch.

Until CORE-V is accepted upstream, code will be developed in narrow mirrors of upstream repositories, featuring a single branch based on upstream top of tree.  This will be used as the basis of the patches to be submitted for upstreaming.

<!--- Partners may maintain local backport branches for corrections to official releases used in their internal HW development flows. --->

Once CORE-V is accepted upstream, common CORE-V specific code for LLVM will be developed and maintained in upstream `llvm.org` repositories.  However, dedicated extensions may continue to be developed in member-specific repositories.

### Summary timeline

- Preliminary Project Launch (PPL, this document): end October 2020

  - includes project plan

- Project Launch (PL): end November 2020
- First release (CVA6 64/32 bits, HW verification equivalence to GCC): end 2020
- Upstreaming of first release: 2021 H1, targeting LLVM 13.0.

The schedule of LLVM releases follows a six-month cycle which additionally depends on the progress of on-going releases.  This makes the scheduling of upstream contributions more flexible (and more difficult to schedule upfront).

- Subsequent releases to support further instruction set extensions will be timed according to available resources.

## OpenHW members/participants committed to participate in this project

All OpenHW Group members are invited to contributed expertise to this project. At present we are aware of the interest and commitment of:

1. Thales
2. Embecosm

## Project Manager and technical lead

- Zbigniew Chamski, Thales (supported by Jean-Roch Coulon, Thales) in close interaction with Jeremy Bennett, Embecosm

This is a much lighter role than a traditional project manager, since the requirements are fully defined through the hardware design process.  The primary roles will be:
- tracking and reporting status to OpenHW Software TG and Technical WG
- identifying and resolving issues related to the outcome.


## Project documents

The following project documents will be created:
- Preliminary Project Proposal (this document)

  - including an initial project plan
  - including an initial risk register

- Project Proposal, an updated version of this document

  - separate detailed project plan
  - separate risk register

## Virtual customer

It is proposed that the OpenHW Group Hardware TG acts as a "virtual customer" to exercise the compiler as it is developed.  In particular, the CVA6 project will exercise the multi-XLEN and core functionality of the compiler in the CVA6 verification flow.

In addition, while the tool chain will have been thoroughly tested, it will benefit from the OpenHW Group Hardware TG being able to use it with their reference applications.

## Summary of requirements

The requirements for the CVA6 platform are captured in ...

## Explanation of why OpenHW should do this project

A processor is only useful with a robust, up-to-date, proven compiler tool chain.  CORE-V, being a strict RISC-V derivative can use a standard RISC-V compiler tool chain, but in these circumstances will not be able to take advantage of any of the extended ISA features of the CORE-V processors.  This project will provide a baseline tool chain allowing these extensions to be used, albeit without optimization, from the CORE-V tool chain.

Furthermore, the licensing scheme of LLVM (Apache license) makes it particularly appealing to industrial players.  Because it does not require the disclosure of compiler source code, chip manufacturers and system integrators can better protect their differentiating Intellectual Property assets.  This licensing scheme also creates additional opportunities for software companies which can provide high added-value services in the area of compiler tuning for hardware/system-centric companies.

## Industry landscape

The "original" compiler used for the development of RISC-V cores is GCC.  However, LLVM offers a licensing scheme that is more appealing to the processor industry as it does not require the disclosure of critical vendor-specific intellectual property (IP) when distributing the software.  LLVM-based compiler chains appear to be at the foundation of many market leader offerings and it is expected that this will also be the case for RISC-V / CORE-V solutions.

### Related efforts to be described

The upstream LLVM tool chain projects already support standard RISC-V.  RISC-V support in LLVM is strongly compliant to the RISC-V specification, but relatively immature in comparison to other architectures as x86, Arm and MIPS.

Currently LLVM does not support CORE-V specific features or extensions, and subsequently, its test suites do not include any CORE-V or PULP-specific tests.

Other toolchains available for RISC-V are:
- the GNU tool chain for RISC-V, for which a CORE-V specific project is being set up within OpenHW Group;
- IAR RISC-V compiler

The GNU tool chain project within OpenHW Group intends to support CORE-V and PULP extensions, but at the time of writing focuses solely on the CV32E40P platform.

## External dependencies

Prequisites:
- all the upstream tool sources
- a suitable platform for regression testing the compiler.
- agreement on the instruction set encodings to be compliant with the RISC-V standard.

External dependencies
- ongoing tracking of upstream until the CORE-V tool chain is accepted upstream.

## List of project outputs (deliverables)

## Project deliverables

### Final deliverables

1. extensions to upstream LLVM compiler tools to support CORE-V; and
2. revisions to the CORE-V design specifications to clarify ambiguities.

### Interim deliverables

1. Reports on progress to the monthly SW TG:
   - progress against work packages;
   - regression test results;
   - updates to the project plan; and
   - updates to the risk register.
2. Continuously updated source code as new features are added.

## TGs impacted/resource requirements

The Software TG will be responsible for oversight of the planning and delivery of this project.

## OpenHW engineering staff resource plan: requirement and availability

- Duncan Bees - program management oversight

## Engineering resource supplied by members - requirement and availability

TBD

*Note:* This is effort by experienced LLVM compiler tool chain specialist engineers.

## OpenHW marketing resource - requirement and availability

- press release support if/when CORE-V is accepted upstream.

## Marketing resource supplied by members - requirement and availability

- No  resource requirements yet identified

## Funding supplied by OpenHW - requirement and availability

- None proposed

## Funding supplied by members - requirement and availability

- An alternative to support in kind is funding of effort by Thales and Embecosm.

## Architecture diagram

The standard LLVM tool chain components are shown in the following diagram.

![](images/gnu-tools.png)

## Who would make use of OpenHW output

See [Explanation of why OpenHW should do this project](#explanation-of-why-openhw-should-do-this-project)

## Project license model

Each component will use the license of the corresponding upstream RISC-V component.

## Description of initial code contribution, if required

- a GNU tool chain with support for CORE_V open hardware loop supplied by Embecosm.

## Repository structure

The respositories are mirrors of the upstream repositories.

## Project distribution model

The delivered code will be distributed as part of the upstream projects.

Pending upstream adoption, the source code for the components will be available through mirror repositories under OpenHW Group GitHub.

## Preliminary risk register

This will become a separate document at full project launch.

Risk is scored as likelihood (1-10) x impact (1-3) with mitigation required for any risk with score of 10 or more, of with an impact of 3 (project killer).

| Risk                          |   L |   I |   R | Mitigation                 |
|:----------------------------- | ---:| ---:| ---:|:---------------------------|
| Insufficient resource available | 5 | 3 | 15 | Socialize around OpenHW members to find expertise or funding. |

## Preliminary project plan

This will become a separate document at full project launch.

The support of any instruction set extension in the GNU compiler tool chain breaks into four work packages.

1. *Addition of the instructions to the GNU assembler and linker.*  The GNU assembler tables are modified to add the missing instructions.   Where new operand types are added, the parser is extended as appropriate.  Tests are added to `testsuite/gas/riscv` to exercise each instruction, including tests for all failure modes.

   If operands may be relocatable symbols, and cannot use existing relocations, the linker is extended with new relocations.

   At the end of this work package, the instructions can be used with the GNU assembler, or within GCC as inline assembly code.

2. *Add intrinsic and/or builtin functions to GCC.*  Intrinsic functions are just wrappers around inline assembly code provided for convenience.  Builtin functions are integral to the compiler, giving greater opportunity for the compiler to optimize seamlessly around the new instructions.  In general builtin functions are to be preferred, but they are more work, and in some cases may make little difference, so intrinsic functions will suffice.

   Builtin or intrinsic functions are added as appropriate.  Tests will be added to `testsuite/gcc.target/riscv` to exercise each function, including tests for all failure modes.

   It should be noted that some instructions may not be suitable for direct intrinsic or builtin functions, particularly if they only make sense within a looping context. In such cases derivative builtin functions using the new instructions may be more useful (for example optimized `memcpy`).

	At the end of this work package the instructions can be used directly from C or C++ via the intrinsic/builtin functions.

3. *Provide instruction generation patterns to GCC.*  GCC is a pattern matching compiler. Patterns in the GCC intermediate representation are used to lower the intermediate statements to RISC-V code.  Patterns will be provided to lower code to use the new instructions where this is advantageous.

   Tests are added  to `testsuite/gcc.target/riscv` to verify that the patterns are being applied correctly. In addition benchmarking, for example with [Embench](https:://embench.org), will be used to measure the benefit obtained by generating the new instructions.

	Some of the instruction set extensions will prove harder than others to generate, particularly for the post-increment instructions.  Getting effective use of auto-increment instructions in a compiler target such as RISC-V, which has been optimized without them, is a challenge.

	At the end of this work package, appropriate C or C++ code will generate object code using the new instructions. It is worth noting that without optimization, the cases where this happens may be limited.

4. *Extend GCC optimization to take advantage of new instructions.*  This is where GCC optimization passes are modified and extended to try to increase the opportunity to use the new instructions.  This may involve changes to existing passes, or writing new passes, and is an inherently open ended task.

   Tests are again be added `testsuite/gcc.target/riscv` to verify that the optimizations are being applied.  Benchmarking is used to measure the benefit obtained by optimization. This is done at the start of this work package, since the results will guide the optimization strategy.

	As with code generation, some of the instruction set extensions will prove harder than others to optimize, particularly for the post-increment instructions.  Optimizing  use of auto-increment instructions in a compiler target such as RISC-V, which has been optimized without them, is a challenge.

	At the end of this work package, the quality of code generated from C or C++ should be improved. It is worth noting that optimization is a very inexact science. It is usual to try 10 or 20 different optimization strategies, of which 20% will have a negative effect, 60% will have a minimal effect and only 20% will make a significant difference.

In addition there are there are two work packages which apply throughout

5. *Continuous integration and test.*  This is an essential prerequisite to any GNU tool chain development.  The project must be able to run the GCC, GNU assembler and GNU linker regression tests and nay benchmarking on a regular basis.  This may use the Hardware TG reference FPGA platform or a simulation model of the platform.

6. *Extend tool chain drivers for CORE-V.*  The GNU compiler tools will build out of the box with any vendor specified, so tools can be named `riscv-corev-elf-* rather than riscv-unknown-elf-*.  The vendor field is used to control whether code for CORE-V extensions is included in the tool chain.

   It is important that the user can control which CORE-V instructions are included, which is achieved through the -march option to the tools. It uses the syntax specified in chapter 27 of the current RISC-V ISA standard.  Vendor specific instruction set extensions are alphabetic and prefixed by X.  We shall provide support for `Xcorev` to provide support for all extensions, and the following to support specific subsets:

   - hardware loop: `Xcorevhwl`
   - multiple accumulate: `Xcorevmac`
   - post-increment and register-indexed load/store: `Xcorevpostinc`
   - direct branches: `Xcorevbi`
   - general ALU operations: `Xcorevalu`

Finally there is a work package to prepare for the future

7. *Support generic instruction set extensions to CORE-V.*  There is no credible automated way to provide an optimizing compiler from a specification of an instruction.  We therefore will define a process by which the compiler can be manuall extended by specialist experts in GCC optimization.

However...

There is an automated technology, CGEN, which allows automatic creation of the assembler, disassembler and instruction set simulator from a semi-formal specification of the architecture in Scheme.  This is widely used for architectures in the GNU tool chain.

CGEN has not been used to generate the assembler/disassembler in upstream RISC-V tools. However the CGEN specification has been written. This project can extend this specification to support CORE-V.  This then provides a faster way to implement work package 1 for future instruction set extensions.

### Program of work

We propose the following work sequence within the scope of Phase 1 of this PPL:

- work package 5;
- work package 6;
- work packages 1, 2 and 3 for hardware loop;
- work packages 1, 2 and 3 for multiple accumulate;
- work packages 1, 2 and 3 for post-increment and register-indexed load/store;
- work packages 1, 2 and 3 for direct branches;
- work packages 1, 2 and 3 for general ALU operations; and
- work package 7.

Note that we do not propose any optimization work as part of this project.
