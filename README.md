# 🏍️ Vehicle Rental

A FiveM ESX resource that lets players rent vehicles from NPC attendants placed around the map. Features a countdown timer UI, NPC dialogue, and server-side payment validation.

---

## 📋 Features

- Configurable rental locations with custom NPC models and vehicle types
- Server-side money checks and deductions via ESX
- NPC dialogue menu using `NpcDialogue`
- 3D proximity UI via `textui`
- In-game countdown timer with NUI overlay
- Auto-deletes vehicle when rental time expires
- Prevents renting while dead or while already renting

---

## 📦 Dependencies

- [es_extended (ESX)](https://github.com/esx-framework/esx_core)
- NpcDialogue
- textui

---

## 🔧 Installation

1. Add the following to your `server.cfg`:
   ```
   ensure VehicleRental
   ```

3. Configure your rental locations in `Config.lua` (see below).

---

## ⚙️ Configuration

Edit `Config.lua` to define your rental stations:

```lua
Config = {
    {
        vehmodel  = 'bf400',            -- Vehicle model to spawn
        model     = 's_m_y_valet_01',   -- NPC ped model
        heading   = 317.0,              -- NPC heading
        modelPos  = vector3(-259.31, -987.17, 31.22),  -- NPC position
        spawnPos  = vector3(-260.97, -992.61, 30.17),  -- Vehicle spawn position
        price     = 800,                -- Rental cost ($)
        rentTime  = 300000,             -- Rental duration (ms) — 300000 = 5 minutes
    },
    -- Add more locations...
}
```

### Config Fields

| Field | Type | Description |
|-------|------|-------------|
| `vehmodel` | `string` | The vehicle model name to rent out |
| `model` | `string` | The NPC ped model to spawn at the location |
| `heading` | `float` | Direction the NPC faces |
| `modelPos` | `vector3` | World position for the NPC |
| `spawnPos` | `vector3` | World position where the vehicle spawns |
| `price` | `number` | Amount of money deducted from the player |
| `rentTime` | `number` | Rental duration in milliseconds |

---

## 🗂️ File Structure

```
VehicleRental/
├── Config.lua       # Rental location configuration
├── Client.lua       # Client-side logic (NPC, UI, vehicle, timer)
├── Server.lua       # Server-side callback (payment, validation)
├── nui/
│   ├── index.html   # NUI overlay (countdown display)
│   ├── script.js    # NUI message handler
│   └── style.css    # NUI style
└── fxmanifest.lua   # Resource manifest
```

---

## 🎮 How It Works

1. Player walks up to a rental NPC (within 2.5 units).
2. An `[E] Rent Vehicle` prompt appears via `textui`.
3. Pressing **E** opens an NPC dialogue menu.
4. Selecting *"I want to rent a vehicle"* triggers a server callback that checks the player's balance and deducts the rental fee.
5. If approved, the vehicle spawns at `spawnPos` and the player is warped in.
6. A countdown timer starts. While the player is **off** the vehicle, the timer ticks down and shows in the NUI overlay.
7. Getting back on the vehicle pauses the countdown (timer resets).
8. When the timer hits zero (or the vehicle is deleted), the vehicle is removed and `rented` is reset.

> ⚠️ The countdown only ticks while the player is **not** in the rented vehicle. Being in the vehicle pauses the timer.

---

## 🖥️ NUI Timer Overlay

The countdown overlay appears when the player exits the rented vehicle. It displays remaining seconds and turns **red** at 30s, 15s, and 5s warnings.

NUI messages used:

| Action | Description |
|--------|-------------|
| `open` | Shows the timer overlay |
| `close` | Hides the timer overlay |
| `time` | Updates the displayed seconds |

---

## 📝 Notes

- Players cannot rent a vehicle while dead.
- Only one active rental is allowed per player at a time.
- If the spawn point is blocked, the rental is cancelled and the player is notified (money is still deducted server-side — handle refunds if needed).
- The vehicle plate is set to `RENT` and colours are applied on spawn.

---

## 📄 License

This project is open source. Feel free to use, modify, and redistribute with credit.
