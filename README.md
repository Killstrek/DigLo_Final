# DigLo_Final — Mini CPU + Stack

CEDT Final Project 2569 · Digital Logic 2110252

An 8-bit multi-cycle CPU built in [Digital](https://github.com/hneemann/Digital). It loads a program into a 256 × 14-bit **pRAM**, runs it from `0x00` until `STOP`, then streams `rRAM[0x00–0x0F]` out on `output` (plus two 7-segment displays).

Full spec (Thai): [`docs/spec.md`](docs/spec.md) · testcase list: [`docs/testcases-and-files.md`](docs/testcases-and-files.md)

## Repository layout

| Path | Contents |
|---|---|
| `template/TEMPLATE_Project_2569.dig` | Course template: I/O pins, pRAM/rRAM, and the 13 starter testcases |
| `circuit/` | Our CPU circuit (`.dig`) goes here |
| `docs/` | Project spec and testcase list (Markdown + original `.docx`) |

## CPU at a glance

- **Registers (8-bit):** accA, accB (accumulators), regC, regD, SP (stack pointer)
- **Flags:** eq, gr, ls — set by `CMP` (signed); `isPrime` updates only eq
- **Stack:** lives in rRAM, full-descending from `0xFF`; `SP = 0x00` means empty
- **Instruction:** 14 bits = opcode `[13:8]` + operand `[7:0]`
- **Rules:** single-address-port RAMs only (no RAM Dual Port), not single-cycle (fetch and execute in separate clocks), GCD and FIB must loop (no ROM/lookup of answers), positive-edge clock

### I/O

| Name | Dir | Bits | Meaning |
|---|---|---|---|
| M, N | in | 8 | Parameters, read only by `regC ← M` / `regD ← N` |
| progIn | in | 14 | Instruction stream while `progLoad = 1` |
| reset | in | 1 | Clear PC, SP, registers, flags, `rRAM[0x00–0x0F]` |
| progLoad | in | 1 | Store `progIn` into pRAM from `0x00` every clock |
| start | in | 1 | `PC = SP = 0`, run |
| result | in | 1 | Stream `rRAM[0x00..0x0F]`, one per clock |
| clk | in | 1 | Positive-edge clock |
| valid | out | 1 | 1 after `STOP` |
| done | out | 1 | 1 after all 16 values have been sent |
| output | out | 8 | Value being sent |

### Opcode map (`opcode[5:4]` = group)

| Group | Set | Instructions |
|---|---|---|
| `00` Data | Immediate / copy / input | NOP, accA←op, accB←op, accA↔accB copies, regC/regD←accA, accA←regC/regD/SP, regC←M, regD←N |
| `01` Memory/Stack | Direct / indirect / stack | accA←rRAM[op], rRAM[op]←accA, accA←pRAM[op][7:0], accA←rRAM[regC], rRAM[regC]←accA, PUSH, POP |
| `10` ALU | Arith / div / logic / shift | ADD, SUB, MUL (4×4 bit), DIV, MOD, AND, OR, XOR, NOT, SHL, SHR, ROL, ROR (by accB[2:0]) |
| `11` Control | Branch / loop / compare / special | JMP, JEQ, JGR, JLS, DJNZ, CMP, isPrime, GCD, FIB, STOP |

## Testcases (starter set, in the template)

| # | Testcase | Tests |
|---|---|---|
| 01 | P01T01 | Load program, add, send result |
| 02 | P02T01 | Unconditional JMP |
| 03 | P03T01 | Read low 8 bits of pRAM |
| 04 | P04T01 | isPrime, general values |
| 05 | P05T01 | isPrime changes only eq; gr keeps its value |
| 06 | P06T01 | Read M and N |
| 07 | P07T01 | Write rRAM through regC (value 2i at address i) |
| 08 | P08T01 | Add, subtract, multiply (low 4 bits only) and overflow |
| 09 | P09T01 | AND OR XOR NOT |
| 10 | P10T01 | Swap M, N through the stack (example in the spec) |
| 11 | P11T01 | DJNZ loop summing 1..N |
| 12 | P12T01 | GCD, general values (accB unchanged) |
| 13 | P13T01 | FIB, result at 0x0A–0x0B |

## Status

- [ ] CPU circuit in `circuit/`
- [ ] All 13 starter testcases pass
- [ ] 70% testcases (released 15 Oct)
- [ ] 100% testcases (released 22 Oct)
- [ ] Technical document (≤ 10 pages): design, ASM/FSM chart, datapath, control unit
- [ ] Group name, IDs, names and roles inside the `.dig`

## Running the testcases

Open the `.dig` in Digital and use **Simulation → Run Tests**, or from a terminal:

```sh
java -cp Digital.jar CLI test -circ circuit/<file>.dig
```

## Key dates (2569)

| Date | Event |
|---|---|
| Thu 15 Oct | 70% testcases (26) released |
| Thu 22 Oct | 100% testcases (34) on the grader |
| Tue 27 Oct, 19:00 | Secret instructions released |
| Thu 29 Oct | Presentations |
| Fri 30 Oct, 24:00 | Grader closes; submit slides, report, `.dig` |
