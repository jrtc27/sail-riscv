A guide to reading the specification
------------------------------------

The model contains the following Sail modules in the `model` directory:

- `prelude.sail` contains useful Sail library functions.  This file
  should be referred to as needed.

- `riscv_types.sail` contains some basic RISC-V definitions.  This
  file should be read first, since it provides basic definitions that
  are pervasively used throughout the specification, such as privilege
  levels, registers and register access, interrupt and exception
  definitions and numbering, and types used to define memory accesses.

- `riscv_sys.sail` describes M-mode and S-mode CSRs, interrupt and
  exception delegation and dispatch, and handling privilege
  transitions.  In addition, this file contains functions to handle
  the reading and writing of CSRs with WPRI, WLRL and WARL fields.
  Since these functions are intended to capture platform-specific
  functionality, future versions of the model might separate these
  functions out into a separate platform-defined file.  The current
  implementation of these functions usually implement the same
  behavior as the Spike emulator.

- `riscv_platform.sail` contains platform-specific functionality for
  the model.  It contains the physical memory map, the local interrupt
  controller, and the MMIO interfaces to the clock, timer and terminal
  devices.  Sail `extern` definitions are used to connect externally
  provided (i.e. external to the Sail model) platform functionality,
  such as those provided by the platform support in the C and OCaml
  emulators.  This file also contains the externally selectable
  options for platform behavior, such as the handling of misaligned
  memory accesses, the handling of PTE dirty-bit updates during
  address translation, etc.

- `riscv_mem.sail` contains the functions that convert accesses to
  physical addresses into accesses to physical memory, or MMIO
  accesses to the devices provided by the platform, or into the
  appropriate access fault.

- `riscv_vmem.sail` describes the S-mode address translation.  It
  contains the definitions and processing of the page-table entries
  and their various permission and status bits, the specification of
  page-table walks, and the selection of the address translation mode.

- `riscv.sail` captures the instruction definitions and their assembly
  language formats.  Each instruction is represented as a variant
  clause of the `ast` type, and its execution semantics are
  represented as a clause of the `execute` function. `mapping` clauses
  specify the encoding and decoding of each instruction to and from
  assembly language formats.

- `riscv_step.sail` implements the top-level fetch and execute loop.
  The `fetch` is done in 16-bit granules to handle RVC instructions.
  The `step` function performs the instruction fetch, handles any
  fetch errors, dispatches the execution of each instruction, and
  checks for any pending interrupts that may need to be handled.  A
  `loop` function implements the execute loop, and uses the same HTIF
  (host-target interface) mechanism as the Spike emulator to detect
  termination of execution.

- `riscv_analysis.sail` is used in the formal operational RVWMO memory
  model.
