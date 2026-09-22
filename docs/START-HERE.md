# Top-Level Control starting material

September 22, 2026. Initial investigations for team discussion; no personal assignments or deadlines.

## Shared starting points

- [Editable architecture diagram](https://github.com/SiliconBadgers/architecture/blob/main/docs/accelerator-diagram.md) and [candidate boundaries](https://github.com/SiliconBadgers/architecture/blob/main/contracts/accelerator-boundaries.md).
- [Workload cases and source shapes](https://github.com/SiliconBadgers/architecture/blob/main/docs/workload-cases.md).
- [Measured llama.cpp report](https://github.com/SiliconBadgers/software/blob/main/experiments/llama-cpp/2026-09-22/REPORT.md) and [reproduction procedure](https://github.com/SiliconBadgers/software/blob/main/experiments/llama-cpp/2026-09-22/README.md).
- [Parallel team investigations](https://github.com/SiliconBadgers/planning/blob/main/docs/team-start.md).

The diagram and engine split are proposals. Start from available shapes and
reference cases now; use explicit parameters or stubs where decisions remain
open. Software's broader profiling study is not a prerequisite. Preserve the
source revision, assumptions, commands and limits of each result. Members and
leads can choose a different investigation that resolves a relevant uncertainty.


## First useful output

A small dependency/scheduler model comparing one command in flight with grouped
commands or limited overlap. Use variable compute and transfer latencies now;
refine them as team measurements arrive. Include a timeline and an explanation
of command granularity, ownership and completion/error boundaries.

## Procedure

1. Walk a projection/activation/projection chain and an attention or recurrent-state chain from the saved workload evidence.
2. Model operation dependencies, buffer ownership, dispatch overhead and outstanding transfers. Distinguish arithmetic done from externally visible output.
3. Compare coarse commands with fine commands using identical operation work and adjustable latencies. Account for packing, transfer and dispatch costs.
4. Add backpressure and injected errors. Check that new issue stops and accepted traffic drains before resources are released.
5. Sketch the local/top-level split with Compute and the host-visible split with SoC and Software.

The source diagram's normal route is IDLE, FETCH, VALIDATE, DISPATCH, EXECUTE,
DRAIN, COMPLETE. Its fuller proposal also names RESET, FAULT DRAIN, FAULT HOLD
and HALTED; ACK releases the terminal command, and reset requires quiescence.
These are behavior to evaluate, not an adopted FSM encoding or final interface.

## First checks

Exercise rejection before execution, long engine stalls, delayed write responses,
duplicate/late completion, fault while a transfer is outstanding, terminal
acknowledgment and reset requests before/after quiescence. State liveness
assumptions explicitly: unbounded backpressure cannot promise a bounded finish.

Put the model, cases and trace/timeline in `experiments/<study>/`. A useful result
identifies where dispatch or ownership serializes work and what overlap is
actually safe. `make test` currently reports an unimplemented scaffold; complete
engine RTL is not required to begin the model.
