# 🧱 RbxKit

> Open-source templates, utilities, and systems for Roblox game development.

![Status](https://img.shields.io/badge/status-active-brightgreen)
![License](https://img.shields.io/badge/license-MIT-blue)
![Luau](https://img.shields.io/badge/language-Luau-orange)

---

## Contents

1. [Overview](#overview)
2. [Game Templates](#game-templates)
3. [Core Modules](#core-modules)
4. [Project Structure](#project-structure)
5. [Quick Start](#quick-start)
6. [API Reference](#api-reference)
7. [Contributing](#contributing)
8. [License](#license)

---

## Overview

RbxKit is a collection of production-ready Roblox development resources — game templates, reusable modules, UI components, and scripting utilities. Everything is written in **Luau** with type annotations and is compatible with **Roblox Studio** and **Rojo**-based workflows.

| Feature | Description |
|---|---|
| 🗂️ Game Templates | Full game scaffolds for common genres |
| ⚙️ Core Systems | DataStore, RemoteEvent bridge, admin framework |
| 🖼️ UI Components | Inventory grids, dialogs, toast notifications |
| 🔧 Dev Utilities | Logger, signal library, scheduler |
| 💰 Monetization | GamePass and Developer Product handlers |
| 📦 Rojo-Ready | Ships with `default.project.json` |

---

## Game Templates

Each template is a standalone Roblox place file plus a Rojo project. Clone, open in Studio, start building.

| Template | Status |
|---|---|
| 🌀 Simulator | `stable` |
| 🏗️ Tycoon | `stable` |
| 🏃 Obby | `stable` |
| 🔫 Round-Based | `stable` |
| 🐾 Pet Collector | `beta` |
| 🏪 Shop System | `stable` |
| 📋 Admin Panel | `stable` |
| 🗺️ Lobby + Map Vote | `beta` |

---

## Core Modules

### DataStore Wrapper

Retry-safe DataStore wrapper with session locking, versioning, and schema migration support.

```lua
--!strict
local PlayerData = require(game.ServerScriptService.RbxKit.DataStore)

PlayerData:Setup({
    schema  = { coins = 0, level = 1, inventory = {} },
    version = 3,
    migrate = function(data, from)
        if from < 2 then data.level = 1 end
        return data
    end,
})

Players.PlayerAdded:Connect(function(plr)
    local data = PlayerData:Load(plr)
    data.coins = data.coins + 100
    PlayerData:Save(plr, data)
end)
```

### Network (RemoteEvent Bridge)

Type-safe client-server communication with automatic argument validation.

```lua
-- shared/Remotes.lua
--!strict
local Net = require(game.ReplicatedStorage.RbxKit.Net)

return Net:Define({
    PurchaseItem   = Net.ServerEvent,
    UpdateCoins    = Net.ClientEvent,
    GetLeaderboard = Net.ServerFunction,
})
```

### Signal Library

Lightweight typed signal/event emitter for decoupled module communication.

```lua
local Signal = require(game.ReplicatedStorage.RbxKit.Signal)

local onCoinCollected = Signal.new()

onCoinCollected:Connect(function(player, amount)
    print(player.Name, "collected", amount, "coins")
end)

onCoinCollected:Fire(player, 50)
```

### Admin System

Chat command framework with permission levels.

```lua
local Admin = require(game.ServerScriptService.RbxKit.Admin)

Admin:SetOwners({ 123456789 }) -- UserId list

Admin:AddCommand("speed", function(player, args)
    local target = Admin:FindPlayer(args[1])
    if target then
        target.Character.Humanoid.WalkSpeed = tonumber(args[2]) or 16
    end
end)
```

---

## Project Structure

```
rbxkit/
├── templates/              # standalone place + rojo project per template
│   ├── simulator/
│   ├── tycoon/
│   └── round-based/
├── modules/                # drop-in Luau modules
│   ├── DataStore.lua
│   ├── Net.lua
│   ├── Signal.lua
│   ├── Admin.lua
│   ├── Scheduler.lua
│   └── Logger.lua
├── ui/                     # ScreenGui components
│   ├── Toast.lua
│   ├── Dialog.lua
│   └── InventoryGrid.lua
├── monetization/
│   ├── GamePass.lua
│   └── DevProduct.lua
└── default.project.json
```

---

## Quick Start

> **Requirements:** Rojo 7+, Roblox Studio. For manual install, download the `.rbxmx` from Releases and drag into Studio's Explorer.

**1.** Clone the repo:
```bash
git clone https://github.com/rbxkit/rbxkit
```

**2.** Open a template folder:
```bash
cd templates/simulator
```

**3.** Serve with Rojo:
```bash
rojo serve default.project.json
```

**4.** Connect Studio via the Rojo plugin and start building.

---

## API Reference

### DataStore

| Method | Side | Description |
|---|---|---|
| `:Setup(config)` | Server | Define schema, version, and migration function |
| `:Load(player)` | Server | Load player data with session lock |
| `:Save(player, data)` | Server | Save data and release session lock |

Auto-saves on `Players.PlayerRemoving` — no manual teardown needed.

### Net

| Method | Side | Description |
|---|---|---|
| `:Define(map)` | Shared | Declare all remotes in one shared module |
| `Net.ServerEvent` | Shared | Client → Server (no return) |
| `Net.ClientEvent` | Shared | Server → Client (no return) |
| `Net.ServerFunction` | Shared | Client → Server → Client (returns value) |

### Signal

| Method | Description |
|---|---|
| `.new()` | Create a new signal |
| `:Connect(fn)` | Register a listener, returns connection |
| `:Once(fn)` | Register a one-shot listener |
| `:Fire(...)` | Fire the signal with arguments |
| `:Destroy()` | Clean up all connections |

### Scheduler

| Method | Description |
|---|---|
| `:Every(seconds, fn)` | Run `fn` on a fixed interval. Returns handle with `:Stop()` |
| `:After(seconds, fn)` | Run `fn` once after a delay |

---

## Contributing

Contributions welcome — bug fixes, new modules, or additional templates.

**Guidelines:**
- All files must start with `--!strict`
- Include type annotations on all public functions
- Add a brief usage example in the module's header comment
- One module per file, clear single responsibility

**Steps:**

1. Fork the repo and create a branch: `git checkout -b feat/my-module`
2. Write Luau with strict types
3. Add your module/template under the appropriate folder
4. Open a pull request with a short description

---

## License

Released under the **MIT License** — free to use in commercial and non-commercial Roblox projects.

See [LICENSE](./LICENSE) for the full text.

---

<p align="center">Made with ☕ for the Roblox dev community</p>
