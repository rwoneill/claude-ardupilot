# ArduPilot Workspace Context

This workspace is for writing and troubleshooting ArduPilot firmware code and Lua scripts that run on ArduPilot autopilots. Claude Code reads this file at the start of every session.

> ## CONFIGURE ME
>
> Replace these values for your project. Everything below this block is the standing context — leave it as-is unless you are deliberately customizing it.
>
> - **Vehicle:** ArduPlane &nbsp;<!-- ArduCopter / ArduPlane / Rover / ArduSub / Blimp / AntennaTracker -->
> - **Target firmware branch / tag:** `Plane-4.6` &nbsp;<!-- Use the exact branch name on github.com/ArduPilot/ardupilot -->
> - **Frame:** QuadPlane, X frame &nbsp;<!-- e.g. Quad/X, Hex/X, Plus, fixed-wing tail-dragger, rover skid -->
> - **Reference aircraft:** _your_aircraft_name_ &nbsp;<!-- short identifier you can reuse across conversations -->
> - **Autopilot hardware:** _e.g. CubeOrangePlus / Pixhawk6C / MatekH743_
> - **Notable peripherals:** _e.g. DroneCAN ESCs (vendor + model), DroneCAN GPS, smart battery, etc._
> - **Sample dataflash log path:** _e.g. `C:\path\to\sample.BIN` — used as a default when the user says "the log"_
> - **Local ArduPilot clone path** (optional): _e.g. `C:\src\ardupilot` — if set, prefer reading from disk over fetching from GitHub_
> - **GCS:** _e.g. Mission Planner / QGroundControl / MAVProxy_

---

## How to answer questions in this workspace

These rules override the default model behavior for ArduPilot questions:

1. **Verify against the upstream source before answering.** ArduPilot APIs, parameters, and Lua bindings change between releases. Memory is unreliable for anything version-specific. Always check:
   - The matching branch on GitHub (URL: `https://github.com/ArduPilot/ardupilot/tree/<branch>`),
   - Or — if a local clone path is configured above — the working tree on disk at the right branch.
2. **Cite the file and line** when explaining behavior, e.g. `libraries/AP_Motors/AP_MotorsMatrix.cpp:142`. The user should be able to click straight to the source.
3. **If you're not sure a parameter, function, or binding exists on the target branch, look it up.** Do not guess from naming conventions. The cost of fetching one URL or grepping one file is much lower than the cost of giving the user a confident wrong answer that bricks their aircraft.
4. **Flag version drift.** If the user is on `Plane-4.6` and you only have evidence from `master`, say so explicitly: "this is from master — confirm on Plane-4.6 before using."
5. **For dataflash logs**, the only trustworthy schema is `libraries/AP_Logger/LogStructure.h` plus the per-vehicle `Log.cpp` on the matching branch. Third-party log parsers — including ones the user may have written — can have stale or wrong field layouts. Cross-check before using.
6. **For Lua bindings**, the only trustworthy reference is `libraries/AP_Scripting/docs/docs.lua` on the matching branch. The Lua sandbox is restricted; many desktop Lua features are not available.
7. **Don't invent flight behavior.** If asked "what does ArduPilot do when X happens," and you don't have the source path that implements it, say "let me find it" and grep — don't reason from first principles.

When you need to fetch something, prefer `https://raw.githubusercontent.com/ArduPilot/ardupilot/<branch>/<path>` for source files. The HTML wiki and forums require fetching the rendered page.

---

## Authoritative resources

Always prefer upstream sources over memory. Bookmark these:

- **Source code (canonical):** <https://github.com/ArduPilot/ardupilot>
- **Raw file URL pattern:** `https://raw.githubusercontent.com/ArduPilot/ardupilot/<branch>/<path>`
- **Main site:** <https://ardupilot.org/>
- **User wiki (per-vehicle):** <https://ardupilot.org/ardupilot/>
  - Plane: <https://ardupilot.org/plane/>
  - Copter: <https://ardupilot.org/copter/>
  - Rover: <https://ardupilot.org/rover/>
  - Sub: <https://ardupilot.org/sub/>
- **Developer wiki:** <https://ardupilot.org/dev/>
- **Parameter reference (per-vehicle):** linked from each vehicle wiki, e.g. <https://ardupilot.org/plane/docs/parameters.html>
- **Lua scripting docs:** <https://ardupilot.org/plane/docs/common-lua-scripts.html> (paths exist for other vehicles too)
- **Lua bindings reference (canonical):** <https://github.com/ArduPilot/ardupilot/blob/master/libraries/AP_Scripting/docs/docs.lua> — but always check the **matching branch** for the version installed on the aircraft.
- **Discuss forum:** <https://discuss.ardupilot.org/> — search here before assuming a bug; QuadPlane, Copter, and tuning each have their own categories.
- **Contributor guide / coding style:** <https://ardupilot.org/dev/docs/contributing.html> and `Tools/CodeStyle.md` in the repo.

When a user question is version-specific (e.g., "in Plane 4.6"), check the matching branch/tag — not `master`.

---

## Vehicle firmware types

ArduPilot is a single repo producing separate firmware per vehicle. Shared code lives in `libraries/`; vehicle-specific code lives in the directories below.

| Firmware | Directory | Purpose |
|----------|-----------|---------|
| Copter | `ArduCopter/` | Multirotors and traditional helicopters |
| Plane | `ArduPlane/` | Fixed-wing (incl. VTOL/QuadPlane) |
| Rover | `Rover/` | Ground vehicles and boats |
| Sub | `ArduSub/` | Underwater ROVs |
| Blimp | `Blimp/` | Lighter-than-air |
| AntennaTracker | `AntennaTracker/` | Ground-based antenna tracking |

QuadPlane code lives in `ArduPlane/quadplane.cpp` and `ArduPlane/quadplane.h`. VTOL motor mixing uses shared `libraries/AP_Motors/` code.

---

## Repository layout (cheat-sheet)

- `ArduCopter/`, `ArduPlane/`, `Rover/`, `ArduSub/`, `Blimp/`, `AntennaTracker/` — per-vehicle main code.
- `libraries/` — shared subsystems: `AP_AHRS`, `AP_Motors`, `AP_GPS`, `AP_Scripting`, `AP_Mission`, `GCS_MAVLink`, `AP_Logger`, `AC_AttitudeControl`, etc.
- `Tools/` — autotest framework, SITL helpers, build scripts.
- `modules/` — git submodules (ChibiOS, mavlink, waf, DroneCAN, etc.).
- `libraries/AP_Scripting/` — Lua engine, bindings, and example scripts:
  - `examples/` — small demo scripts.
  - `applets/` — production-ready features.
  - `drivers/` — sensor / peripheral drivers in Lua.
  - `docs/docs.lua` — **canonical Lua bindings list**.
- `libraries/AP_Logger/LogStructure.h` — **canonical dataflash message schema**.

When looking for an example or binding, `libraries/AP_Scripting/examples/` and `libraries/AP_Scripting/applets/` are the first places to check.

---

## Build system (waf)

ArduPilot uses **waf**. Common workflow:

```
./waf configure --board <BOARD>     # e.g. sitl, CubeOrange, Pixhawk6C, MatekH743, CubeOrangePlus
./waf <vehicle>                      # plane / copter / rover / sub / blimp / antennatracker
./waf --targets bin/<binary>         # specific target, e.g. bin/arduplane
./waf list_boards                    # enumerate supported boards
./waf clean                          # wipe build dir
```

Build output: `build/<board>/bin/`. Always `./waf configure --board <BOARD>` first when switching boards.

---

## SITL (Software-in-the-Loop)

Primary way to test firmware and Lua scripts without hardware:

```
Tools/autotest/sim_vehicle.py -v ArduPlane -f quadplane --console --map
Tools/autotest/sim_vehicle.py -v ArduPlane -f quadplane -w        # wipe EEPROM
Tools/autotest/sim_vehicle.py -v ArduCopter                       # other vehicles
```

- Integrates with MAVProxy (CLI), Mission Planner, QGroundControl as GCS.
- External physics: Gazebo, RealFlight, X-Plane, JSBSim, FlightGear, Webots.
- Autotest framework: `Tools/autotest/autotest.py` (used by CI).

For Lua development, SITL + MAVProxy is the fastest iteration loop: drop scripts into the SITL `scripts/` directory, set `SCR_ENABLE 1`, reload.

---

## Lua scripting

- Enabled with parameter `SCR_ENABLE = 1` (requires reboot to expose further `SCR_*` params).
- Scripts live in `APM/scripts/` on the SD card (or the SITL equivalent). `.lua` files auto-load at boot.
- Requires ≥2 MB flash and ≥80 KB free RAM on the autopilot.
- Sandboxed in parallel with flight code; the flight loop is never blocked by scripts.
- Standard execution pattern:
  ```lua
  function update()
      -- read state, act
      return update, 1000   -- reschedule in 1000 ms
  end
  return update, 1000
  ```
- Common binding namespaces: `ahrs`, `battery`, `gps`, `rc`, `SRV_Channels`, `param`, `vehicle`, `arming`, `mission`, `logger`, `gcs`, `relay`, `serial`, `CAN`, `Parameter`, `Location`, `Vector3f`.
- Vehicle-specific bindings differ — flight modes, `vehicle:*` getters, etc. Check `<Vehicle>/mode.h` for mode IDs.
- **Canonical binding list:** `libraries/AP_Scripting/docs/docs.lua` — always check the matching branch.
- Examples: `libraries/AP_Scripting/examples/`, applets: `libraries/AP_Scripting/applets/`.
- Debug via `gcs:send_text(severity, msg)` — appears in the GCS messages pane. Severities: 0 EMERGENCY through 7 DEBUG.

The Lua sandbox is **not** desktop Lua. No `io`, no `os.execute`, no arbitrary `require`. Only the listed bindings are available — confirm against `docs.lua` before writing.

---

## MAVLink & ground control

- **Protocol:** MAVLink v2. Definitions: `modules/mavlink/message_definitions/v1.0/`. ArduPilot uses `ardupilotmega.xml`, which extends `common.xml`.
- **GCS options:** Mission Planner (most complete, Windows), QGroundControl (cross-platform), MAVProxy (CLI, scripting-friendly, used in SITL), APM Planner 2.
- MAVLink is the boundary for companion computers, telemetry, and parameter access.

---

## Parameters

- Primary tuning/config interface. Defined in each vehicle's `Parameters.cpp` and in libraries' `var_info` tables.
- Online reference (per vehicle): linked from <https://ardupilot.org/>. Always check the version matching the firmware on the aircraft.
- From Lua: `param:get('SCR_ENABLE')` / `param:set('RTL_ALT', 1500)` / `param:set_and_save(...)`.
- **Don't invent parameter names.** If unsure whether `FOO_BAR` exists, grep `Parameters.cpp` for the relevant vehicle on the target branch.

---

## Dataflash logs

- Binary format (`.bin` / `.BIN`) written to the SD card under `APM/LOGS/`.
- **Schema source of truth:** `libraries/AP_Logger/LogStructure.h` plus per-vehicle `Log.cpp` on the matching branch. Third-party parsers can have stale or wrong layouts — cross-check before trusting them.
- Common parser tools:
  - `pymavlink` `DFReader` API (Python) — most flexible.
  - `mavlogdump.py` (from pymavlink, when bundled) — quick CLI dump.
  - MAVExplorer (from MAVProxy) — interactive exploration.
  - Mission Planner log review — graphical.
- Quickly enumerate message types in a log: `python -m pymavlink.tools.mavlogdump --types <file>` if available, or via the Python API.
- Key message types worth knowing across vehicles:
  - `ATT` — attitude (roll/pitch/yaw, desired vs actual).
  - `RATE` — body rate (Copter mostly).
  - `CTUN` — control tuning (fixed-wing primary).
  - `QTUN` — QuadPlane VTOL tuning (Plane only).
  - `PIDR / PIDP / PIDY / PIDA` — rate / attitude PID inputs and outputs.
  - `MODE` — flight mode changes with reason code.
  - `MSG` — text messages including `gcs:send_text` output and internal status.
  - `EV` — events (arm, disarm, takeoff, etc.).
  - `ERR` — subsystem errors with subsystem ID + error code.
  - `GPS`, `BAT`, `EKF*`, `VIBE`, `IMU`, `RCIN`, `RCOU`, `ESC` — self-explanatory.
- The `ESC` message carries telemetry from DShot/DroneCAN ESCs (RPM, current, voltage, ESC temp, motor temp, error rate). Logging rate is set by `LOG_FILE_RATEMAX_ESC` and similar — check before assuming high-rate data is available.

When parsing, sample Python skeleton (no CLI tools required):

```python
from pymavlink import mavutil
m = mavutil.mavlink_connection(LOG_PATH, dialect="ardupilotmega")
while True:
    mm = m.recv_match(blocking=False)
    if mm is None: break
    if mm.get_type() == "ESC":
        # mm.Instance, mm.RPM, mm.Curr, mm.Volt, mm.Temp, ...
        pass
```

---

## Troubleshooting workflow

When diagnosing an in-flight issue:

1. **Reproduce in SITL first** if possible — removes hardware variables.
2. **Confirm firmware version** from the log's first few `MSG` lines (`ArduPlane V<version> (<hash>)`) before trusting any parameter or binding advice.
3. **Walk the `MODE` and `MSG` streams** for context: when did mode change, what GCS messages were emitted, any `ERR` events.
4. **Inspect the relevant high-rate streams** for the symptom: attitude (`ATT`), motor commands (`RCOU`), ESC telemetry (`ESC`), EKF (`XKF*`/`NKF*`), vibration (`VIBE`), GPS (`GPS`).
5. **Compare against a known-good flight** if one exists. Asymmetries (one motor hotter, one PID saturating) often reveal the failure.
6. **Search the discuss forum** (<https://discuss.ardupilot.org/>) before declaring a bug — most issues have prior threads.
7. **When proposing a fix or a parameter change, cite source or wiki.** "Set `Q_FOO` to 5" is not a complete answer; the source/wiki link or `Parameters.cpp:line` is.

---

## Coding conventions (upstream)

- C++ style: `Tools/CodeStyle.md` and `.clang-format` at repo root.
- Contributor guide: <https://ardupilot.org/dev/docs/contributing.html>.
- Match the pattern of surrounding code — naming, error handling, no new dependencies, no blocking calls in the main loop.
- Real-time constraints matter: anything in the main loop runs at hundreds of Hz. No I/O, no allocations, no waiting.

---

## When writing code in this workspace

- **Lua** scripts target the ArduPilot scripting sandbox, not desktop Lua. Confirm every binding against `docs.lua` on the target branch.
- **C++** changes must compile against the matching branch — do not assume `master` APIs exist in stable releases.
- **Parameters and bindings:** verify before referencing.
- **Log parsing:** the matching-branch `LogStructure.h` is the only trustworthy reference.
- **Patches:** preserve the file's existing style, indentation, and licensing header. Don't reformat unrelated code.

If you find yourself about to write something ArduPilot-specific without a source reference, stop and look it up.
