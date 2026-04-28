---
name: ardupilot-helper
description: ArduPilot firmware, Lua scripting, parameter, and dataflash log reference. Activate for any question about ArduCopter, ArduPlane, QuadPlane, Rover, ArduSub, MAVLink, pymavlink, .bin log analysis, motor mixers, autopilot hardware (CubeOrange, Pixhawk, MatekH743, etc.), or related autopilot topics. Also activate when the user references a Plane-4.x / Copter-4.x / Rover-4.x branch, ArduPilot parameters (Q_*, RTL_*, SCR_*, etc.), or asks about Lua bindings (ahrs:, gcs:, param:, mission:, vehicle:).
---

# ArduPilot reference skill

This skill provides authoritative ArduPilot context and citation rules. Claude loads it automatically when ArduPilot-related topics come up.

The same content is also used as a project-level `CLAUDE.md` for users running Claude Code inside an ArduPilot project folder. For Claude Code, save this file as `CLAUDE.md` in the project root (without the YAML frontmatter at the top).

> ## CONFIGURE ME (optional, but improves answer quality)
>
> Fill these in for your aircraft if you know the values. If you skip this, the skill still works — Claude will just ask follow-up questions when it needs vehicle specifics.
>
> - **Vehicle:** _e.g. ArduPlane_   <!-- ArduCopter / ArduPlane / Rover / ArduSub / Blimp / AntennaTracker -->
> - **Target firmware branch / tag:** _e.g._ `Plane-4.6`   <!-- Use the exact branch name on github.com/ArduPilot/ardupilot -->
> - **Frame:** _e.g. QuadPlane, X frame_
> - **Reference aircraft:** _your aircraft name_
> - **Autopilot hardware:** _e.g. CubeOrangePlus / Pixhawk6C / MatekH743_
> - **Notable peripherals:** _e.g. DroneCAN ESCs (vendor + model), DroneCAN GPS, smart battery_
> - **Sample dataflash log path:** _e.g._ `C:\path\to\sample.BIN` _— used as a default when the user says "the log"_
> - **Local ArduPilot clone path** (optional): _e.g._ `C:\src\ardupilot` _— if set, prefer reading from disk over fetching from GitHub_
> - **GCS:** _e.g. Mission Planner / QGroundControl / MAVProxy_

---

## How to answer questions when this skill is active

These rules override default model behavior:

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

- **Source code (canonical):** https://github.com/ArduPilot/ardupilot
- **Raw file URL pattern:** `https://raw.githubusercontent.com/ArduPilot/ardupilot/<branch>/<path>`
- **Main site:** https://ardupilot.org/
- **User wiki (per-vehicle):** https://ardupilot.org/ardupilot/
  - Plane: https://ardupilot.org/plane/
  - Copter: https://ardupilot.org/copter/
  - Rover: https://ardupilot.org/rover/
  - Sub: https://ardupilot.org/sub/
- **Developer wiki:** https://ardupilot.org/dev/
- **Parameter reference (per-vehicle):** linked from each vehicle wiki, e.g. https://ardupilot.org/plane/docs/parameters.html
- **Lua scripting docs:** https://ardupilot.org/plane/docs/common-lua-scripts.html
- **Lua bindings reference (canonical):** https://github.com/ArduPilot/ardupilot/blob/master/libraries/AP_Scripting/docs/docs.lua — but always check the **matching branch** for the version installed on the aircraft.
- **Discuss forum:** https://discuss.ardupilot.org/
- **Contributor guide / coding style:** https://ardupilot.org/dev/docs/contributing.html and `Tools/CodeStyle.md` in the repo.

When a user question is version-specific (e.g., "in Plane 4.6"), check the matching branch/tag — not `master`.

---

## Vehicle firmware types

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

---

## Build system (waf)

```
./waf configure --board <BOARD>     # e.g. sitl, CubeOrange, Pixhawk6C, MatekH743, CubeOrangePlus
./waf <vehicle>                      # plane / copter / rover / sub / blimp / antennatracker
./waf --targets bin/<binary>         # specific target, e.g. bin/arduplane
./waf list_boards                    # enumerate supported boards
./waf clean                          # wipe build dir
```

Build output: `build/<board>/bin/`.

---

## SITL

```
Tools/autotest/sim_vehicle.py -v ArduPlane -f quadplane --console --map
Tools/autotest/sim_vehicle.py -v ArduPlane -f quadplane -w        # wipe EEPROM
Tools/autotest/sim_vehicle.py -v ArduCopter                       # other vehicles
```

For Lua development, SITL + MAVProxy is the fastest iteration loop: drop scripts into the SITL `scripts/` directory, set `SCR_ENABLE 1`, reload.

---

## Lua scripting

- Enabled with parameter `SCR_ENABLE = 1` (requires reboot).
- Scripts live in `APM/scripts/` on the SD card. `.lua` files auto-load at boot.
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
- **Canonical binding list:** `libraries/AP_Scripting/docs/docs.lua` — always check the matching branch.
- Debug via `gcs:send_text(severity, msg)`. Severities: 0 EMERGENCY through 7 DEBUG.

The Lua sandbox is **not** desktop Lua. No `io`, no `os.execute`, no arbitrary `require`. Only listed bindings are available.

---

## MAVLink & ground control tooling

MAVLink is the external boundary for ArduPilot — companion computers, telemetry radios, ground stations, and routers all speak it.

**MAVLink protocol**

- Site / message reference: https://mavlink.io/en/
- Message catalog: https://mavlink.io/en/messages/
- Source (definitions, generators): https://github.com/mavlink/mavlink
- ArduPilot uses MAVLink v2 with the `ardupilotmega.xml` dialect, which extends `common.xml`. Definitions in the ArduPilot repo: `modules/mavlink/message_definitions/v1.0/`.

**Mission Planner** — feature-rich Windows GCS, the most complete option for ArduPilot.

- Docs: https://ardupilot.org/planner/
- Source: https://github.com/ArduPilot/MissionPlanner
- Forum category: https://discuss.ardupilot.org/c/mission-planner/

**QGroundControl** — cross-platform GCS (Windows / macOS / Linux / Android / iOS), simpler interface than Mission Planner.

- Site: https://qgroundcontrol.com/
- Docs: https://docs.qgroundcontrol.com/
- Source: https://github.com/mavlink/qgroundcontrol

**MAVProxy** — CLI ground station, scripting-friendly, used by SITL and the autotest framework.

- Docs: https://ardupilot.org/mavproxy/
- Source: https://github.com/ArduPilot/MAVProxy
- Companion library `pymavlink` (Python bindings + log parsing): https://github.com/ArduPilot/pymavlink

**MAVLink Router** — multiplex MAVLink between multiple endpoints (vehicle, ground station, companion computer, telemetry radio). Useful when one telemetry stream needs to feed several consumers.

- Source: https://github.com/mavlink-router/mavlink-router

**APM Planner 2** — older cross-platform GCS, less actively developed; mostly a fallback if Mission Planner and QGroundControl don't fit.

- Docs: https://ardupilot.org/planner2/

When answering MAVLink-specific questions (message fields, units, expected ranges), prefer the canonical message definitions at https://mavlink.io/en/messages/ or the matching XML in `modules/mavlink/message_definitions/v1.0/` on the target branch — message field meanings sometimes differ slightly between dialects, and ArduPilot adds custom messages in `ardupilotmega.xml`.

---

## Parameters

- Defined in each vehicle's `Parameters.cpp` and in libraries' `var_info` tables.
- Online reference linked from https://ardupilot.org/. Always check the version matching the firmware on the aircraft.
- From Lua: `param:get('SCR_ENABLE')` / `param:set('RTL_ALT', 1500)` / `param:set_and_save(...)`.
- **Don't invent parameter names.** Grep `Parameters.cpp` for the relevant vehicle on the target branch when unsure.

---

## Dataflash logs

- Binary format (`.bin` / `.BIN`) under `APM/LOGS/`.
- **Schema source of truth:** `libraries/AP_Logger/LogStructure.h` plus per-vehicle `Log.cpp` on the matching branch.
- Common parser tools: `pymavlink` `DFReader` API (most flexible), `mavlogdump.py` (when bundled), MAVExplorer, Mission Planner log review.
- Key message types: `ATT`, `RATE`, `CTUN`, `QTUN`, `PIDR/P/Y/A`, `MODE`, `MSG`, `EV`, `ERR`, `GPS`, `BAT`, `EKF*`, `VIBE`, `IMU`, `RCIN`, `RCOU`, `ESC`.
- The `ESC` message carries DShot/DroneCAN ESC telemetry (RPM, current, voltage, ESC temp, motor temp, error rate). Logging rate is set by `LOG_FILE_RATEMAX_ESC` and similar.

Sample Python (no CLI tools required):

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

If running in a chat-only context (no Bash / Python execution), describe what to run and tell the user to paste back the output.

---

## Troubleshooting workflow

1. **Reproduce in SITL first** if possible.
2. **Confirm firmware version** from the log's first few `MSG` lines before trusting any advice.
3. **Walk the `MODE` and `MSG` streams** for context.
4. **Inspect the relevant high-rate streams** for the symptom: `ATT`, `RCOU`, `ESC`, `XKF*`, `VIBE`, `GPS`.
5. **Compare against a known-good flight** if one exists.
6. **Search the discuss forum** before declaring a bug.
7. **When proposing a fix or parameter change, cite source or wiki.**

---

## When writing code

- **Lua** scripts target the ArduPilot scripting sandbox, not desktop Lua. Confirm every binding against `docs.lua` on the target branch.
- **C++** changes must compile against the matching branch — don't assume `master` APIs exist in stable releases.
- **Parameters and bindings:** verify before referencing.
- **Log parsing:** the matching-branch `LogStructure.h` is the only trustworthy reference.

If you find yourself about to write something ArduPilot-specific without a source reference, stop and look it up.
