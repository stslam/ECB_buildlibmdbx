# Build scripts

Requires **Embarcadero RAD Studio 13.1** (or compatible) with both the
Win32 (`bcc32c`) and Win64 (`bcc64x`) C++ toolchains installed.

| Script          | Target | Compiler  | Librarian   |
|-----------------|--------|-----------|-------------|
| `mk_rd.bat`     | Win32  | `bcc32c`  | `tlib`      |
| `mk_rd64.bat`   | Win64  | `bcc64x`  | `llvm-lib`  |
| `all.bat`       | both, Debug + Release, in sequence |||

`bcc64x` is the modern Clang-based Win64 compiler — do not confuse its
install path with `bcc32c`'s; they live under separate `bin` / `bin64`
directories.

## Before running

Both `mk_rd.bat` and `mk_rd64.bat` hardcode two things near the top —
edit them for your machine:

```bat
SET "PRJDIR=..."          :: path to the patched libmdbx source tree
                           :: (must contain mdbx.c, mdbx.c++, mdbx-internals.h, mdbx.h)

SET "BCBENV32=..."        :: rsvars.bat / rsvars64.bat path
SET "COMPILER=..."        :: bcc32c.exe / bcc64x.exe path
SET "TLIB=..."            :: tlib.exe (Win32 only)
SET "LLVM_LIB=..."        :: llvm-lib.exe (Win64 only)
```

Defaults in the scripts match a RAD Studio 13.1 install at
`C:\Program Files (x86)\Embarcadero\Studio\37.0\`.

## Usage

```bat
mk_rd.bat            :: Win32 Release (default)
mk_rd.bat release     :: Win32 Release (explicit)
mk_rd.bat debug       :: Win32 Debug

mk_rd64.bat           :: Win64 Release (default)
mk_rd64.bat release   :: Win64 Release (explicit)
mk_rd64.bat debug     :: Win64 Debug

all.bat               :: all four combinations, in sequence
```

With logging:

```bat
mk_rd.bat release > build.log 2>&1
```

## Output

```
%PRJDIR%\OUT\Win32\Debug\LIB\mdbx.lib
%PRJDIR%\OUT\Win32\Release\LIB\mdbx.lib
%PRJDIR%\OUT\Win64\Debug\LIB\mdbx.lib
%PRJDIR%\OUT\Win64\Release\LIB\mdbx.lib
```

(plus `TEMP\*.obj` / `TEMP\*.o` intermediate object files, not needed
after the `.lib` is produced).

## Compile flags of note

- `mdbx.c` is compiled with `-DMDBX_WITHOUT_MSVC_CRT=1`; `mdbx.c++` is
  **not** — the C++ wrapper needs the standard C++ runtime (new/delete,
  exceptions), which the CRT-less path doesn't provide.
- `-DMDBX_BUILD_FLAGS="\"bcc32c Win32 %BUILD_TYPE%\""` (or the bcc64x/Win64
  equivalent) suppresses libmdbx's "build flags undefined" warning at
  compile time — purely cosmetic, safe to drop if you don't care about it.
