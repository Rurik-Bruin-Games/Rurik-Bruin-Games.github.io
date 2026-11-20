# Vision BASIC vs. Commodore 64 BASIC V2: Reference Guide

A comprehensive comparison of syntax, data types, math rules, memory handling, and commands between **Vision BASIC** and standard **C64 BASIC V2**.

---

## 1. Execution & Architecture

| Feature | Commodore 64 BASIC V2 | Vision BASIC |
| :--- | :--- | :--- |
| **Execution Type** | Interpreted in real time from ROM. | Compiled directly into standalone 6510 machine language. |
| **Distribution** | Requires BASIC V2 ROM (built into every C64). | Standalone `.PRG` binaries; runs on any stock C64 without Vision BASIC loaded. |
| **Development RAM** | Runs on a stock 64K Commodore 64. | Requires at least **256K of expanded RAM** (REU, GeoRAM, or SuperCPU) to develop/compile. |
| **Assembly Integration** | Requires `DATA` statements, `POKE` loops, and `SYS` calls. | Native inline 6510 assembler (`[ LDA #0 : STA $D020 ]`) mixed directly on BASIC lines. |
| **Program Structure** | Line numbers are mandatory for every line and `GOTO`/`GOSUB`. | Line numbers are optional on non-target lines; supports named `LABEL` / `TAG` targets. |
| **Compound Statements (`:`) & Line Length** | Up to 80 characters (2 screen lines); almost any commands can be chained with `:` on one line. | **78-character physical line limit.** Simple commands can be chained with `:`, but multi-line block keywords (`IF...ELSE...ENDIF`, `DO...LOOP`, `WHILE...WEND`, `REPEAT...UNTIL`, `PROC...RETURN`) and directives (`HALT`, `RESUME`) must start on their own separate physical lines. |
| **Comments & Code Skipping** | `REM` per line (takes up runtime RAM and slows execution). No block skip directive. | `REM` per line (or `;` inside `[ ... ]` assembly, which consumes the rest of the line); stripped at compile time for zero runtime overhead. Use `HALT` and `RESUME` at the start of lines to skip compiling blocks of code. |

---

## 2. Variables, Strings & Data Types

| Feature | Commodore 64 BASIC V2 | Vision BASIC |
| :--- | :--- | :--- |
| **Variable Initialization (`CLR`)** | Automatically clears all variables to `0`/`""` every time `RUN` is executed. | **Does not auto-clear variable RAM at startup.** Always put **`CLR`** on your first line (before `DIM` or string assignments) to zero out variable memory—otherwise, uninitialized string pointers contain garbage addresses and assigning a string (`A$="HI"`) can overwrite compiled code and crash the C64. |
| **Default Numeric Type** | 40-bit (5-byte) Floating-Point (`A`). | 32-bit (4-byte) Integer (`A`). |
| **Integer Range** | `-32,768` to `+32,767` (16-bit signed). | `-16,777,215` to `+16,777,215` (24-bit magnitude + sign byte). |
| **Decimal / Float Type** | Automatic for all variables without `%` or `$`. | Must be explicitly declared using `DECIMAL A` or `DEF DECIMAL A` before use. |
| **Decimal Precision** | ~9 significant digits (floating-point). | Fixed-point: up to **7 decimal places**, max range `±16,777,215`. |
| **The `%` Suffix** | Forces a 16-bit integer (`A%`). `A` and `A%` are different types. | **Does nothing.** Treated as a regular letter in the variable name (`A` and `A%` are simply two separate default-integer variables). |
| **The `$` Suffix** | Defines a string variable (`A$`). | Defines a string variable (`A$`). The **only** active type suffix in Vision BASIC. |
| **Allowed Name Characters** | Letters (`A-Z`) and digits (`0-9`). | Letters, digits, plus `!`, `@`, `#`, `%`, `&`, and `?` anywhere after the first character. |
| **Significant Name Length** | Only the **first 2 characters** are recognized (`SCORE` = `SCUBA`). | Up to **8 characters** are recognized (`PLAYER1` ≠ `PLAYER2`). *(Avoid reserved Vision BASIC keywords like `CODE` as variable names.)* |
| **Variable Scope (`LOCAL` / `GLOBAL`)** | All variables are strictly global. | Supports `LOCAL` and `GLOBAL` compiler scope switches inside `PROC` subroutines. Placing `LOCAL` switches subsequent variables to local scope; you **must** execute `GLOBAL` after `SEND` and before `RETURN` to restore global scope for the rest of the program. |
| **Arrays (`DIM`)** | Supports multi-dimensional arrays (`DIM A(10,10)`) and variable sizes (`DIM C$(B)`). `DIM A(10)` creates floating-point arrays; `DIM A%(10)` creates integer arrays. | **One-dimensional (1D) arrays with fixed literal integer sizes only** (`DIM C$(B)` is not allowed; reserve max capacity with a literal number like `DIM C$(50)`). `DIM A(10)` creates an integer array; `DIM DECIMAL A(10)` creates a decimal array. Simulate 2D grids with a flat 1D array (`IDX = ROW * WIDTH + COL : A(IDX)`), calculating the index into a variable first. |
| **Safe Scratch RAM (`832` / `$0340` Cassette Buffer)** | Cassette buffer (`828–1019` / `$033C–$03FB`, sprite block 13 at `832`) and `$C000–$CFFF` are classic "safe" areas for sprites and ML routines. | **Unsafe—reserved by Vision BASIC.** The cassette buffer (`828–1019`), zero page (`$02–$8F`), and `$0200–$033B` are actively used by Vision BASIC's runtime, math routines, and REU/GeoRAM bank-switching stubs. Use `MOBPAT`/`CODE` for sprites, `DIM` arrays for buffers, or check `FILE` -> Memory for free RAM. |
| **String & Number Joining** | `?TYPE MISMATCH ERROR` if using `+` between a string and a number (`PRINT A$+64` fails; requires `;` or `STR$()`). | **Allowed in `PRINT`.** `PRINT A$+64` automatically converts and prints `64` immediately after `A$` without an error or leading space. |
| **`PRINT` Commas, `TAB()` & `SPC()`** | Commas (`PRINT P,`) jump to 10-column tab zones. `TAB(n)` and `SPC(n)` include an implicit trailing semicolon (`PRINT TAB(10) "HI"` stays on the same line without `;`). | **No 10-column tab zones** (trailing `,` acts like a newline). **`TAB(X)` and `SPC(X)` require an explicit trailing `;`** (`PRINT TAB(X); "HI"` or `PRINT SPC(X);`) to prevent a line feed from cancelling the tab/space. |
| **`VAL()` Function** | Can be used inline inside commands (`PRINT VAL(A$)` or `POKE 1024, VAL(A$)`). | **Must be assigned to a variable first** (`X = VAL(A$) : PRINT X`). Cannot be printed or passed directly inside other commands. Declare `DECIMAL X` first if parsing fractional numbers. |
| **`ASC()` Function** | Accepts string literals (`ASC("A")`) and inline calls (`PRINT ASC("A")`). | **Requires a string variable and variable assignment** (`A$ = "A" : X = ASC(A$) : PRINT X`). Passing a literal string (`ASC("A")`) or printing `ASC(A$)` inline triggers `?SOMEONE GOOFED ERROR`. |
| **Keyboard Input (`INPUT` / `GET`)** | Supports inline prompts and semicolons (`INPUT "NAME";A$` or `INPUT ;A$`). Only `GET A$` (non-blocking) is built in. | **No prompt strings or semicolons in `INPUT`** (`INPUT ;A$` throws a `TYPE MISMATCH` error). Use `PRINT "PROMPT: ";` followed by `INPUT A$`. Includes both `GET A$` (non-blocking) and `GETKEY A$` (waits for a single keypress). |

---

## 3. Math & Order of Operations

| Feature | Commodore 64 BASIC V2 | Vision BASIC |
| :--- | :--- | :--- |
| **Order of Operations** | Standard PEMDAS (`↑`, then `*` `/`, then `+` `-`). | **Strictly left-to-right** (`1 + 2 * 3` equals `9`, not `7`). |
| **Parentheses `()`** | Supported to group equations (`A = (1 + 2) * 3`). | **Not allowed** in math expressions. Complex math must be broken into separate steps. |
| **Inline Math in Commands (`POKE` / `PEEK`)** | Allowed anywhere (`PRINT 1 + 2 * 3`, `POKE 1024 + X, PEEK(1024 + X) + 1`). | **Not allowed in command parameters, including `POKE` and `PEEK` addresses.** Expressions like `POKE 1024 + X, 1` or `V = PEEK(1024 + X)` trigger an error. Calculate addresses and values into variables first (`ADDR = 1024 + X : V = PEEK(ADDR) + 1 : POKE ADDR, V`). |
| **Scientific Notation** | Supported (`1.5E+04`, `2E-3`). | **Not supported.** Numbers must be written out explicitly within range. |
| **Exponents (`↑` / `^`)** | Supported (`A = 2 ↑ 8`). | **Not supported.** Must use repeated multiplication or a `FOR...NEXT` loop. |

---

## 4. Control Flow & Subroutines

| Feature | Commodore 64 BASIC V2 | Vision BASIC |
| :--- | :--- | :--- |
| **`FOR...NEXT` Data Types** | Supports floating-point counters and decimal `STEP` values (`FOR I = 0.5 TO 5.5 STEP 0.5`). | **Integers only.** Both the loop counter and `STEP` value must be whole numbers (`DECIMAL` variables are not allowed). |
| **`FOR...NEXT` Bounds** | Allows negative start and end bounds (`FOR I = -5 TO 5`). | **Non-negative bounds only (`0` or higher).** Negative start/end values trigger an `ILLEGAL VALUE` error. Negative integers are only allowed on `STEP` (`FOR I = 10 TO 1 STEP -1`). |
| **User-Defined Functions & Procedures** | Single-line numeric formulas only via `DEF FNA(X) = ...`, called inline as `FNA(5)`. No multi-line functions, parameters, or local variables. | Multi-line procedures defined with **`PROC Name. A, B`** (or `PROC Name` followed by `PASS A, B` on the next line). Use **`LOCAL`** for local variables, **`SEND Result`** to return a value, and switch back with **`GLOBAL`** right before **`RETURN`**. Call with **`Name. ANS = X, Y`** (or `Name. X, Y` for void procedures). |
| **Returning Strings** | N/A (all variables are global). | Strings and string variables **cannot** be returned via `SEND` from subroutines/procedures; assign to a shared global string variable instead. |
| **Structured Loops** | Only `FOR...NEXT` and `IF...GOTO`. | Adds `DO...LOOP`, `WHILE...WEND`, `REPEAT...UNTIL`, and multi-line `IF...ELSE...ENDIF`. |

---

## 5. Unsupported BASIC V2 Commands & Functions

The following standard C64 BASIC V2 keywords and reserved variables are **omitted** from Vision BASIC (primarily due to the removal of ROM floating-point routines):

* **Reserved System Variables (`ST`, `TI`, `TI$`):** Omitted as reserved variables (treated as ordinary user variables). Use `PEEK(144)` to check the Kernal I/O status byte (`64` = EOF, `128` = device not present), and use `CLOCK` instead of `TI`.
* **Trigonometry & Logarithms:** `SIN`, `COS`, `TAN`, `ATN`, `LOG`, `EXP`
* **Algebraic & Utility Math:** `SQR`, `↑` (exponentiation), `FN`, `DEF` (user math functions; `DEF` is repurposed for variable declarations, and functions use `PROC` / `PASS` / `SEND` / `RETURN`)
* **Logical / Bitwise:** `NOT` (bitwise/logical inversion must be handled via `EOR` or assembly)
* **System / Execution:** `CONT` (cannot continue stopped compiled code), `FRE` (replaced by memory manager commands), `POS`, `USR` (replaced by native inline assembly and `SYS`)

---

## 6. Disk & Editor Differences

| Feature | Commodore 64 BASIC V2 | Vision BASIC |
| :--- | :--- | :--- |
| **Viewing Disk Directory (`DIR`)** | **No `DIR` command.** Requires `LOAD "$",8` followed by `LIST`, which overwrites your BASIC program in memory. | Built-in **`DIR`** command displays the disk directory directly to the screen without wiping your program in RAM. *(Note: Do not type `DIR 8` for drive 8; a number after `DIR` routes output to a **printer** device like `DIR 4`. In v1.2+, `LOAD "$",8` also mimics `DIR` safely.)* |
| **Overwriting Files (`SAVE`)** | Throws `63, FILE EXISTS` if the filename already exists on disk; requires scratching the old file first or using the risky `@0:` prefix (`SAVE "@0:FILE",8`). | **Overwrites automatically.** `SAVE "FILE"` replaces an existing file on disk directly without requiring `@0:`. (Use `SAVE` for source code and `COMP` / `COMPILE` for standalone `.PRG` binaries.) |
| **Deleting Files on Disk** | No dedicated command; requires opening command channel 15 (`OPEN 15,8,15,"S:FILE":CLOSE 15`). | Use **`DISK "S:FILE"`** (or `DISK "S:FILE", 9` for a specific drive). Do not use `DELETE`, which only deletes program lines from RAM (`DELETE 100-200`, or `DELETE` alone like `NEW`). |
| **Storage Devices & Tape Support** | Supports Datasette tape (`device 1`) and disk drives (`8`–`30`). Specify device number on each command (`SAVE "FILE",1` or `LOAD "FILE",8`). | **Disk drives only (`8`–`11`).** Set active drive with `DEVICE 8` (valid range `8`–`11`; device `1` / Datasette or numbers outside `8`–`11` throw an `ILLEGAL VALUE` error for `DEVICE`, `SAVE`, `LOAD`, and `COMP`). |
| **Listing Program Code (`LIST`)** | `LIST` scrolls continuously (hold `CTRL` to slow down; `RUN/STOP` to abort). Supports ranges (`LIST 100-200`). Can also be executed inside a running program to self-list and halt. | **Immediate mode only** (cannot be called inside a compiled program). Automatically **pauses page-by-page** when the screen fills—press **`SPACE`** for the next page, **`RETURN`** to advance one line, or **`RUN/STOP`** (`ESC`) to exit. Supports ranges (`LIST 100-200`) and expands `,` indentation shortcuts. |
| **Printing Program Listings (`PLIST` / `LLIST`)** | Requires manually opening channel 4 and redirecting output (`OPEN 4,4 : CMD 4 : LIST : PRINT#4 : CLOSE 4`). | Built-in **`PLIST`** (e.g., `PLIST` or `PLIST 100-500`) sends program listings straight to the printer. **`LLIST`** outputs extended program info (`LLIST 100-500, 1` routes it to the printer), and **`LISTER`** prints a saved program directly from disk. *(Data printed from inside a running program still uses `OPEN 4,4` / `PRINT#4`.)* |
| **Switch Disk Drive** | Specify device number on every command (`LOAD "FILE",9`). | Set active drive once with `DEVICE 9`, then run `DIR`, `LOAD`, or `SAVE`. |
| **Initialize / DOS Command** | `OPEN 15,8,15,"I0":CLOSE 15` | `DISK` or `DISK "I0"` |
| **`FILE` Menu & Navigation** | No built-in file/memory manager screen. | Type `FILE` to open the Memory, Banks, Details, and Settings screen (`F1`–`F7` / cursor keys switch tabs). Press **`RUN/STOP`** (`ESC` in VICE) to close and return to the editor. |
| **Hard Exit / Re-Entry** | `RUN/STOP` + `RESTORE` resets BASIC V2 variables and screen. | `QUIT` or `RUN/STOP` + `RESTORE` (`ESC` + `Page Up` in VICE) exits Vision BASIC back to standard C64 BASIC V2. Cannot be re-entered via `SYS`; you must reload Vision BASIC from disk. |
| **Syntax Error Message** | Displays `?SYNTAX ERROR`. | Displays **`?SOMEONE GOOFED ERROR`** when a syntax mistake or reserved keyword conflict occurs. |
| **Editor Indentation** | Manual spaces only. | Type `,` immediately after a line number with no space (`100,PRINT "HI"`) to auto-indent on `RETURN`/`LIST`. |
| **Subroutine Comment Blocks** | Typed manually line-by-line with `REM`. | Immediate command `DESC 1000, NAME` auto-generates a formatted `REM` header block and label. |
| **Command Abbreviations** | Shift-letter abbreviations (`?` for `PRINT`, `lI` for `LIST`). | Type full commands or use built-in editor shortcuts; `VERSION` displays compiler build. |

---

## 7. Major Features Added in Vision BASIC (100+ New Commands)

* **Graphics & Bitmaps:** `HIRES`, `MULTI`, `PLOT`, `LINE`, `BOX`, `CIRCLE`, `PAINT`, `CLS`, `COLORS`, `VIDLOC`, `BLANK`, `NORMAL`.
* **Sprites (MOBs):** `MOB`, `ALLMOBS`, `MOBSET`, `MOBCLR`, `MOBXY`, `MOBCOL`, `MOBMULTI`, `MOBSIZE`, `DETECT`.
* **Sound (SID Chip):** `SIDCLR`, `VOICE` (must be set before voice commands), `FREQ`, `WAVE`, `ADSR`, `PULSE`, `FILTER`, `CUTOFF`, `VOL`.
* **Input & Timers:** `JOY`, `POLL` (selects joystick port), `PADDLE`, `CLOCK` (jiffy timer).
* **Raster Interrupts:** `INTERRUPT`, `STARTINT`, `INTEND`, `HALTINT`, `RASTER`, `CATCH` (waits for specific scanline), `PANX`, `PANY`.
* **Memory & Banks:** `BANK`, `MEMCPY` / `COPY`, `FILL`, `CODE`, `FILE` (memory/bank manager).
* **Extensibility:** `MODULE` and user-defined commands allow saving reusable code libraries and custom BASIC keywords.

---

## 8. Quick Code Examples

### 1. Safe Startup, Variables & Left-to-Right Math

```basic
10 CLR
20 DECIMAL PRICE, TAX, TOTAL
30 PRICE = 19.95
40 TAX = PRICE * 0.13
50 TOTAL = PRICE + TAX
60 PRINT "TOTAL: $"+TOTAL
70 END
```

* **`10 CLR`:** Always initialize variable RAM first to prevent uninitialized string pointers from crashing the machine.
* **`20 DECIMAL`:** Explicitly declares fixed-point decimal variables (default variables are 32-bit integers).
* **`60 PRINT`:** Joining a string and a number with `+` works directly in `PRINT` without `STR$()`.

---

### 2. Procedures (`PROC`), Scope (`LOCAL`/`GLOBAL`) & Return Values (`SEND`)

```basic
10 CLR
20 X = 12 : Y = 8
30 ADDNUMS. ANS = X, Y
40 PRINT "RESULT: "+ANS
50 END

100 PROC ADDNUMS. A, B
110 LOCAL
120 C = A + B
130 SEND C
140 GLOBAL
150 RETURN
```

* **`30 ADDNUMS. ANS = X, Y`:** Calls the procedure with a period attached to its name, passing `X` and `Y` and storing the returned value in `ANS`.
* **`110 LOCAL` ... `140 GLOBAL`:** Switches the compiler to local variable scope inside the procedure, returns `C` via `SEND`, and restores `GLOBAL` scope right before `RETURN`.

---

### 3. Inline 6510 Machine Language

```basic
10 CLR
20 PRINT "PRESS ANY KEY TO FLASH BORDER"
30 GETKEY A$
40 [ LDX #0 ]
50 [ LOOP: STX $D020 : INX : BNE LOOP ]
60 END
```

* **`30 GETKEY A$`:** Waits for a single keypress without needing a `GET A$ : IF A$="" THEN` loop.
* **`40`–`50 [ ... ]`:** Embeds native 6510 assembly directly inside BASIC lines with zero `DATA` or `POKE` overhead.

---

### 4. Hardware Sprites (`MOB`)

```basic
10 CLR
20 MOBCLR
30 MOBPAT 0, 1
40 CODE 255,255,255, 255,255,255, 255,255,255
50 MOB 0, 1, 0, 0, 160, 120, 0, 0
60 MOBCOL 1, 0, 0
70 FOR X = 100 TO 250
80 MOBXY X, 120, 0, 0
90 CATCH 250
100 NEXT X
110 MOBCLR
120 END
```

* **`20 MOBCLR`:** Clears all sprite registers at startup (and again on line 110 when finished).
* **`30 MOBPAT 0, 1` & `40 CODE ...`:** Points the `CODE` pointer to sprite shape `0` in bank `1` and writes raw sprite bitmap bytes into memory.
* **`50 MOB ...` & `80 MOBXY ...`:** Activates sprite `0` in single-color mode at `(160, 120)` and moves it smoothly across the screen synced to raster line `250` via `CATCH 250`.

---

### 5. Raster Interrupts (`INTERRUPT`, `STARTINT`, `INTEND`, `HALTINT`)

```basic
10 CLR
20 INTERRUPT 100, 1000
30 PRINT "RASTER SPLIT ACTIVE - PRESS ANY KEY"
40 GETKEY A$
50 HALTINT
60 POKE 53280, 14
70 END

1000 STARTINT
1010 POKE 53280, 0
1020 CATCH 180
1030 POKE 53280, 6
1040 INTEND 0
```

* **`20 INTERRUPT 100, 1000`:** Triggers a custom raster interrupt on scanline `100` (valid range `50`–`249`) that executes the routine starting at line `1000`.
* **`1000 STARTINT` ... `1040 INTEND 0`:** Begins the interrupt handler, changes border colors mid-frame using `CATCH 180`, and exits cleanly to the system IRQ routine (`INTEND 0`).
* **`50 HALTINT`:** Always disable custom raster interrupts with `HALTINT` before exiting your program to prevent crashes.

---

### 6. SID Sound Synthesis (`SIDCLR`, `VOICE`, `VOL`, `ADSR`, `FREQ`, `WAVE`)

```basic
10 CLR
20 SIDCLR
30 VOL 15, 0, 0, 0, 0
40 VOICE 1
50 ADSR 0, 9, 0, 0
60 FREQ 7382
70 WAVE 1, 1, 0, 0
80 FOR T = 1 TO 60 : CATCH 250 : NEXT T
90 WAVE 0, 1, 0, 0
100 SIDCLR
110 END
```

* **`20 SIDCLR`:** Resets all SID chip sound registers cleanly at startup (and again on line 100 when finished).
* **`30 VOL 15, 0, 0, 0, 0`:** Sets master SID volume to `15` (max) with low-pass, band-pass, high-pass filters, and voice 3 disconnect set to `0`.
* **`40 VOICE 1`:** Selects SID voice `1` (`1`–`3`)—this **must** be called before `ADSR`, `FREQ`, `PULSE`, or `WAVE`.
* **`50 ADSR 0, 9, 0, 0` & `60 FREQ 7382`:** Configures Attack/Decay/Sustain/Release (`0`–`15` each) and sets the 16-bit SID frequency value (`7382` ≈ A4 / 440 Hz on NTSC).
* **`70 WAVE 1, 1, 0, 0` & `90 WAVE 0, 1, 0, 0`:** Opens the gate (`1`) using waveform `1` (`1`=triangle, `2`=sawtooth, `4`=pulse, `8`=noise), holds for 60 frames (~1 second), then closes the gate (`0`) to release the note.

---

## 9. Unsafe & Reserved RAM Map

While classic C64 BASIC V2 programmers routinely store sprites or machine-language routines in the cassette buffer (`828–1019` / `$033C–$03FB`) or upper RAM, Vision BASIC actively uses several of these regions for its compiler, 32-bit runtime, and expanded-memory (REU/GeoRAM) bank-switching code.

| Memory Range (Decimal / Hex) | Classic C64 BASIC V2 Status | Vision BASIC Status & Safe Alternatives |
| :--- | :--- | :--- |
| **`0–1` (`$0000–$0001`)** | 6510 CPU Data Direction & I/O Port. | **Unsafe to alter blindly.** Vision BASIC banks BASIC ROM (`$A000–$BFFF`) out to store compiled code and variables underneath. Altering `$01` (`1`) without restoring its exact state will crash compiled code. |
| **`2–255` (`$0002–$00FF`)** | Zero Page (`$FB–$FE` / `251–254` free; `$02–$8F` used by BASIC ROM). | **Unsafe without saving/restoring.** Vision BASIC uses `$02–$8F` for its 32-bit math accumulator, string descriptors, and `LOCAL`/`GLOBAL` scope stack. Only `$FB–$FE` (`251–254`) are generally free—always push/pull on the stack if used inside `STARTINT` interrupts. |
| **`512–827` (`$0200–$033B`)** | Input buffer, Kernal vectors (`$0314–$0319`), and system work area. | **Unsafe.** Holds Kernal IRQ/NMI vectors and Vision BASIC's runtime hooks (`INTERRUPT`, `HALTINT`, and the clean return-to-editor stub). |
| **`828–1019` (`$033C–$03FB`)** | **Cassette Buffer** — classic "safe" scratchpad (e.g., sprite block 13 at **`832` / `$0340`**). | **Unsafe.** Actively used by Vision BASIC's compiler, runtime routines, and REU/GeoRAM bank-switching stubs. Writing to `832` (`$0340`) corrupts Vision BASIC's workspace. Use `MOBPAT`/`CODE` for sprites instead. |
| **`2049–53247` (`$0801–$CFFF`)** | BASIC program text, variables, arrays, and strings (top-down from `$A000`). | **Managed by Vision BASIC.** Compiled machine code starts at `$0801` (`2049`), followed immediately by variables, arrays (`DIM`), and the string heap. Reserve buffers with `DIM` or check **`FILE` -> Memory** before hardcoding `POKE` addresses. |
| **`49152–53247` (`$C000–$CFFF`)** | Classic 4K free RAM block for machine language routines. | **Reserved by default.** Used by Vision BASIC for compiler tables, sprite patterns (`MOBPAT`), character sets, and bitmap screens unless relocated. |
| **`57344–65535` (`$E000–$FFFF`)** | RAM under Kernal ROM (often used for custom charsets/bitmaps). | **Reserved by Vision BASIC.** Used for compiler workspace and graphics buffers unless explicitly configured in `FILE` settings. |

---

## 10. Expanded RAM & VIC-II Bank Switching (`BANK`)

Vision BASIC relies on **expanded RAM** (REU, GeoRAM, or SuperCPU—at least 256K required) to develop and compile programs, and uses `BANK` switching to manage both VIC-II 16K video banks and expanded RAM storage.

### VIC-II 16K Video Banks vs. Expanded RAM Banks

| Concept | Standard C64 BASIC V2 | Vision BASIC |
| :--- | :--- | :--- |
| **VIC-II 16K Video Banks (`0`–`3`)** | Switched manually via CIA2 port (`POKE 56576, (PEEK(56576) AND 252) OR B`) plus `POKE 53272` for screen/character offsets. | Managed via **`BANK`** and graphics/sprite commands (`VIDLOC`, `MOBPAT`, `HIRES`, `MULTI`). Bank `0` (`$0000–$3FFF`), Bank `1` (`$4000–$7FFF`), Bank `2` (`$8000–$BFFF`), and Bank `3` (`$C000–$FFFF`). |
| **Expanded RAM Banks (REU / GeoRAM)** | No native support; requires custom assembly routines writing to `$DF00` (REU) or `$DE00`/`$DFFE` (GeoRAM). | Managed automatically by the compiler and inspected via the **`FILE` -> Banks** tab. Stores source code, symbol tables, and asset banks outside main 64K C64 RAM during editing and compiling. |
| **Standalone Runtime Distribution** | Runs on any stock 64K C64. | Compiled `.PRG` files run on a **stock 64K C64** without expanded RAM—unless your compiled program explicitly uses REU/GeoRAM runtime expansion features. |

### Key Rules for Bank Switching (`BANK`)

* **Select the Active Bank First:** Use **`BANK B`** before placing raw data with `CODE`, configuring sprite pointers (`MOBPAT`), or setting bitmap/character locations (`VIDLOC`).
* **Keep VIC-II Character ROM in Mind:** In VIC-II Banks `0` (`$0000–$3FFF`) and `2` (`$8000–$BFFF`), the VIC-II chip sees the hardware **Character ROM** at `$1000–$1FFF` and `$9000–$9FFF` instead of underlying RAM. Placing custom character sets or sprite patterns in Bank `1` (`$4000–$7FFF`) or Bank `3` (`$C000–$FFFF`) avoids Character ROM shadowing.
* **Inspect Memory Layout with `FILE`:** Run **`FILE`** in immediate mode and check the **Memory** and **Banks** tabs to verify which banks hold your compiled code, variables, and graphics screens before switching banks manually.
* **Restore Default Bank Before Exiting:** If you switch VIC-II banks or screen locations (`VIDLOC`) during your program, restore Bank `0` and default screen memory (`1024` / `$0400`) before `END` so the Vision BASIC editor screen displays properly upon exit.

### Example: Placing Data in a Specific Bank (`BANK` & `CODE`)

```basic
10 CLR
20 BANK 1
30 MOBPAT 0, 1
40 CODE 255,255,255, 129,0,129, 255,255,255
50 BANK 0
60 END
```

* **`20 BANK 1` & `30 MOBPAT 0, 1`:** Selects Bank `1` (`$4000–$7FFF`, where Character ROM does not shadow RAM) and points sprite pattern `0` there.
* **`50 BANK 0`:** Restores Bank `0` cleanly before ending the program.
