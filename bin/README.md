# Pre-built binaries

Drop the built `mdbx.lib` into the matching folder:

```
Win32/Debug/mdbx.lib      <-  OUT\Win32\Debug\LIB\mdbx.lib
Win32/Release/mdbx.lib    <-  OUT\Win32\Release\LIB\mdbx.lib
Win64/Debug/mdbx.lib      <-  OUT\Win64\Debug\LIB\mdbx.lib
Win64/Release/mdbx.lib    <-  OUT\Win64\Release\LIB\mdbx.lib
```

Each `.lib` was built with the `build/` scripts in this repo, against
`stslam/libmdbx_embarcadero` (branch `work_on`) with the patches in
`patches/` applied. See `CHANGELOG.md` for the exact commit/version
each release corresponds to.

Do not commit `.obj`/`.o`/`.bak`/`TEMP/` — only the final `.lib` files
(already covered by `.gitignore`).
