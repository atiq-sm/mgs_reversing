## Cursor Cloud specific instructions

This is a PS1 reverse engineering / decompilation project for *Metal Gear Solid: Integral*. It is a pure offline build-and-verify pipeline (no web servers, databases, or Docker).

### Prerequisites (already installed in snapshot)

- Python 3 with pip packages from `build/requirements.txt`
- Wine (Ubuntu package) and wibo (lightweight Wine alternative at `/usr/local/bin/wibo`)
- PSY-Q SDK cloned to `/psyq_sdk`

### Building

Build commands are run from `build/` directory. The build script `build.py` generates `build.ninja` and then runs ninja.

**Critical gotcha:** Wine crashes when ninja runs compiler steps (`cc1psx.exe`) in parallel inside this container environment. After running `python3 build.py --psyq_path=/psyq_sdk` (which generates `build.ninja` but fails at the ninja step), you must run ninja with `-j1`:

```
cd build
python3 build.py --psyq_path=/psyq_sdk   # generates build.ninja (will fail at ninja step)
~/.local/bin/ninja -j1                     # run ninja single-threaded
```

Alternatively, for the VR executable:
```
python3 build.py --variant=vr_exe --psyq_path=/psyq_sdk
~/.local/bin/ninja -j1
```

### Verification

After building, verify output matches original game binaries:
```
python3 compare.py ../obj/_mgsi.exe
```

All output hashes should show `OK: ... matches target hash`.

### Key paths

- Build scripts: `build/`
- C source: `source/`
- Assembly (not yet decompiled): `asm/`
- Build output (main_exe): `obj/`
- Build output (vr_exe): `obj_vr/`
- PSY-Q SDK: `/psyq_sdk` (pass `--psyq_path=/psyq_sdk` to `build.py`)

### No lint or automated test suite

This project has no linter or test framework. Verification is done by SHA-256 hash comparison of built binaries against originals via `compare.py`.
