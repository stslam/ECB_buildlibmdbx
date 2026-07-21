# BCB_buildlibmdbx

Build tooling, source patches, and pre-built static libraries for using
[libmdbx](https://libmdbx.dqdkfa.ru) with **Embarcadero RAD Studio /
C++ Builder** (bcc32c for Win32, bcc64x for Win64).

Tested with: **RAD Studio 13.1**.

This repo does **not** contain a copy of libmdbx source code. It contains:

- `patches/` — unified diffs to apply on top of libmdbx sources
- `build/` — the `.bat` scripts that drive `bcc32c`/`bcc64x`/`tlib`/`llvm-lib`
- `bin/` — pre-built `mdbx.lib` for Win32/Win64, Debug/Release

It targets the source tree maintained at
[stslam/libmdbx_embarcadero](https://github.com/stslam/libmdbx_embarcadero)
(branch `work_on`), which itself tracks the upstream
[libmdbx](https://sourcecraft.dev/dqdkfa/libmdbx) project.

## Why a separate repo

The fork above gets periodically re-synced from upstream, which has in the
past silently reverted the Embarcadero-specific fixes (see `patches/README.md`
for the exact history). Keeping the patches, build scripts, and binaries here
— decoupled from the fork's source tree — means:

- the fix history survives upstream re-syncs,
- binaries can be published without publishing a full libmdbx source mirror,
- the build scripts can be versioned/fixed independently of source drift.

## Quick start

1. Get the libmdbx source (either the fork above, or upstream + apply patches
   yourself — see `patches/README.md`).
2. Apply the patches in `patches/` on top of it, if not already applied.
3. Edit `PRJDIR` (and, if needed, the Embarcadero install paths) at the top of
   `build/mk_rd.bat` and `build/mk_rd64.bat` to match your machine.
4. Run the build scripts from a directory containing the patched `mdbx.c`,
   `mdbx.c++`, `mdbx-internals.h`, `mdbx.h`:

   ```bat
   :: Win32, Release
   mk_rd.bat release > build.log 2>&1

   :: Win32, Debug
   mk_rd.bat debug > build.log 2>&1

   :: Win64, Release
   mk_rd64.bat release > build.log 2>&1

   :: Win64, Debug
   mk_rd64.bat debug > build.log 2>&1
   ```

   Or build everything in one go:

   ```bat
   build\all.bat
   ```

5. Output: `mdbx.lib` under `OUT\Win32\<Config>\LIB\` and
   `OUT\Win64\<Config>\LIB\` respectively.

## Using the pre-built binaries instead

If you don't want to build it yourself, `bin/` has ready `mdbx.lib` files:

```
bin/Win32/Debug/mdbx.lib
bin/Win32/Release/mdbx.lib
bin/Win64/Debug/mdbx.lib
bin/Win64/Release/mdbx.lib
```

Link the one matching your platform/config, and use the matching `mdbx.h` /
`mdbx.h++` headers from the patched source tree (headers are not duplicated
in this repo — grab them from the fork above, at the same commit noted in
`patches/README.md`).

## Status / disclaimer

Unofficial, community-maintained, not affiliated with the libmdbx author.
Provided as-is, matching Apache-2.0 (see `LICENSE`, `NOTICE`).

## License

Apache-2.0, same as libmdbx. See `LICENSE` and `NOTICE`.
