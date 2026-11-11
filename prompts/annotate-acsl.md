Write complete, mathematically sound ACSL annotations for the target C function(s) and verify them with Frama-C.

Follow these rules strictly:
1. **Header Contract (`include/*.h`):** Add `requires`, `assigns`, and `ensures` clauses above the public function declaration.
   - Include `\valid` or `\valid_read` for all pointer arguments.
   - Include `\separated` whenever two pointer arguments could alias.
   - Ensure `assigns` lists every modified memory location (or `\nothing` for pure functions).
2. **Loop Annotations (`src/*.c`):** For every `for` or `while` loop, add:
   - `loop invariant` covering all loop variables and modified array bounds.
   - `loop assigns` listing every variable and memory range mutated inside the loop.
   - `loop variant` proving loop termination.
3. **Verification Loop:** Run `make verify` in the terminal after adding annotations.
   - Confirm `Eva` reports **0 alarms**.
   - Confirm `WP` proves **100% of goals** (`Valid`).
   - If any goal is `Unknown` or times out, strengthen the loop invariants or helper assertions rather than weakening the function contract.
