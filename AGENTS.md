# AGENTS.md

## Project Overview
Portable C99 software project targeting Linux and macOS (x86_64 and Apple Silicon arm64). All core modules are formally verified for memory safety using Frama-C `Eva` and functional correctness using Frama-C `WP` with ACSL annotations.

## Environment & Tooling
- **Supported Platforms:** Linux and macOS only (do not add Windows-specific code or MSVC pragmas).
- **Required Tools:** `nix`, GNU `make`, `cc` (`gcc` or `clang`), `compiledb`, `frama-c`, and SMT solvers (`alt-ergo` or `z3`).

## Build & Verification Commands
Always run these commands from the repository root:
- **Build binary:** `make`
- **Clean build artifacts:** `make clean`
- **Regenerate compilation database:** `make compile_commands.json`
- **Run full formal verification (`Eva` + `WP`):** `make verify`

## Verification Workflow (Required Before Finishing)
Whenever you add or modify C source or header files:
1. Run `make` to confirm clean compilation with zero warnings (`-Wall -Wextra`).
2. Run `make verify` to execute Frama-C `Eva` and `WP`.
3. Ensure `Eva` reports **0 alarms** and `WP` reports **100% Valid goals**. Do not leave unproved goals or suppressed alarms behind.

## Repository Structure
- `Makefile`: Root build orchestration, `compile_commands.json` generation, and Frama-C verification targets.
- `compile_commands.json`: Auto-generated compilation database (do not edit manually).
- `include/`: Public `.h` headers containing function declarations and primary ACSL function contracts (`requires`, `assigns`, `ensures`).
- `src/`: Implementation `.c` files containing internal helpers and ACSL loop annotations (`loop invariant`, `loop assigns`, `loop variant`).
- `tests/`: Verification harnesses and test drivers.

## Coding & ACSL Standards
- **C Standard:** Strictly adhere to ISO C99 (`-std=c99`). Stick to standard headers (`<stdint.h>`, `<stdbool.h>`, `<stddef.h>`, `<stdlib.h>`, `<string.h>`).
- **Cross-Platform Flags:** Never add OS-specific compiler flags (such as `-arch`, `-framework`, or `-march=native`) to `Makefile`, as they break Frama-C's C parser across Linux and macOS.
- **ACSL Placement:** Place public function contracts (`requires`, `assigns`, `ensures`) in `.h` header files above declarations so callers and `WP` can verify modularly.
- **Loop Annotations:** Every `for` and `while` loop must include explicit `loop invariant`, `loop assigns`, and `loop variant` clauses so `WP` can prove termination and state preservation.
- **Memory & Pointers:** Explicitly specify `\valid`, `\valid_read`, and `\separated` preconditions for all pointer parameters.
- **C File Structure** Do not implement more than one function in the same C file; name the file after the function it implements.

## Hard Boundaries
- **Never** edit `compile_commands.json` by hand; always regenerate it via `make compile_commands.json` when files or flags change.
- **Never** use `bear` on macOS; always rely on `compiledb -n make -B` as configured in the `Makefile`.
- **Never** use `setjmp`, `longjmp`, or global variables
- **Never** weaken or delete existing ACSL `ensures` or `requires` clauses just to make `WP` pass unless the functional specification itself has changed.
- **Always** when a repository’s documentation and its existing code disagree, follow the conventions used
in the existing code, and in your final reply point out each inconsistency you found between the
documentation and the code.
