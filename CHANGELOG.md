# Changelog

## v0.1.0 — initial release

- Patches: fix `YieldProcessor()` guard regression and atomic union
  `_least`-type bug in `mdbx-internals.h`; add missing
  `DEFINE_ENUM_FLAG_OPERATORS` reset to `mdbx.h`. See `patches/README.md`.
- Build scripts for RAD Studio 13.1: `mk_rd.bat` (Win32/bcc32c),
  `mk_rd64.bat` (Win64/bcc64x), `all.bat`. `MDBX_WITHOUT_MSVC_CRT` no
  longer applied to the `mdbx.c++` compile step.
- Pre-built `mdbx.lib`: Win32/Win64 × Debug/Release, built against
  `stslam/libmdbx_embarcadero@782981a` (branch `work_on`) with the
  patches above applied.
