# Patches

Two source patches, both gated behind `__CODEGEARC__` — no effect on
GCC/Clang/MSVC builds.

Base commit these were generated against:
`stslam/libmdbx_embarcadero@782981abd02a142900fc23d928e9c32972fbabcb`
(branch `work_on`).

## Apply

From the root of the libmdbx source tree:

```sh
git apply /path/to/BCB_buildlibmdbx/patches/0001-mdbx-internals.h.patch
git apply /path/to/BCB_buildlibmdbx/patches/0002-mdbx.h.patch
```

or, without git:

```sh
patch -p1 < /path/to/BCB_buildlibmdbx/patches/0001-mdbx-internals.h.patch
patch -p1 < /path/to/BCB_buildlibmdbx/patches/0002-mdbx.h.patch
```

If the fork has already re-synced past this commit, the patches may not
apply cleanly with `git apply` — use `patch` with fuzz, or re-locate the
three spots described below by hand (they're small and easy to find via
`grep -n "__CODEGEARC__"`).

## 0001-mdbx-internals.h.patch

Two independent fixes:

1. **`YieldProcessor()` guard.** Originally introduced correctly in
   `e0ee8bed` as `defined(__CODEGEARC__) && defined(_WIN32) && !defined(YieldProcessor)`.
   A later upstream amalgamated-source regeneration
   (`fc8b8e46`, "set of minor refines mostly by AI suggestions") changed
   `defined(_WIN32)` to `IS_WINDOWS`. `IS_WINDOWS` is not usable at this
   point in the header — build fails. Patch restores `defined(_WIN32)`.

2. **Atomic union member types.** `mdbx_atomic_uint32_t` /
   `mdbx_atomic_uint64_t` use `atomic_uint32_t` / `atomic_uint64_t` under
   `__CODEGEARC__ && __clang__`. This has been wrong since the type was
   first introduced (`e0ee8bed`) — at this point in Embarcadero's
   Dinkumware `<stdatomic.h>`, only the `_least` variants
   (`atomic_uint_least32_t` / `atomic_uint_least64_t`) are declared yet;
   the exact-width aliases aren't visible, so the build fails. Patch
   switches both to the `_least` types.

## 0002-mdbx.h.patch

`mdbx.h` includes `<winnt.h>` independently of `mdbx-internals.h`, which
re-establishes the Windows SDK's non-constexpr `DEFINE_ENUM_FLAG_OPERATORS`
/ `CONSTEXPR_ENUM_FLAGS_OPERATIONS` in that translation unit.
`mdbx-internals.h` already resets these after its own `<winnt.h>` /
`<winternl.h>` include, but the equivalent reset was never added to
`mdbx.h`, so `mdbx.h`'s own constexpr-correct version (guarded by
`#if !defined(DEFINE_ENUM_FLAG_OPERATORS)`) never installs when `mdbx.h`
is used/included on its own. Patch adds the matching `#undef` block.

## Not included here

Two more fixes from the same investigation are **not** patches because
they either already live directly in the upstream/fork source, or belong
to the build scripts (now tracked in `build/` in this repo instead of as
a diff):

- `mdbx.c`: `(size_t)` cast around `ws_lower`/`ws_upper` in the
  `SetProcessWorkingSetSize` warning, and `(int)(...)` cast around the
  `%.*s` precision ternary in `snprintf` — both already present and
  survived upstream re-syncs as of the base commit above.
- `-DMDBX_WITHOUT_MSVC_CRT=1` must be passed when compiling `mdbx.c`,
  but **not** `mdbx.c++` (the C++ wrapper needs the standard C++ runtime).
  This is enforced directly in `build/mk_rd.bat` / `build/mk_rd64.bat`.
