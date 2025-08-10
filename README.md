# MultiWorldManager Plugin Wiki

Welcome to **MultiWorldManager**, an advanced multi-world management plugin for Bukkit/Spigot Minecraft servers. This plugin helps server administrators easily create, import, load, and configure multiple worlds, supporting custom settings for gamemode, PVP, difficulty, spawn points, and respawn worlds. It supports automatic importing and loading of worlds from the configuration, with per-world customization.

## Introduction

MultiWorldManager is designed to simplify multi-world management on Minecraft servers. It automatically detects and imports existing world folders, loads worlds from the configuration, and applies per-world settings (such as enforcing gamemode on world change). It supports creating worlds with custom environments (e.g., NORMAL, NETHER) and types (e.g., FLAT, AMPLIFIED).

- **API Version**: 1.13+
- **Dependencies**: None (core Bukkit/Spigot)

This plugin is ideal for servers requiring multiple dimensions, survival/creative separation, or event worlds.

## Features

- **Automatic World Management**:
  - Automatically import existing world folders (containing level.dat) into the configuration on startup or reload.
  - Automatically load worlds defined in the configuration.
- **World Creation and Import**:
  - Create new worlds with specified environments and types.
  - Manually import world folders.
- **Per-World Settings**:
  - Customize gamemode, PVP, and difficulty.
  - Set spawn points (including coordinates, yaw, and pitch).
  - Define respawn worlds (with a default fallback if missing).
- **Teleportation**:
  - Teleport players to the spawn point of a specified world.
- **Player Management**:
  - Enforce gamemode on world change or join (bypassable with permission).
  - Custom respawn logic: Prioritize bed/respawn anchor; otherwise, respawn at the configured world's spawn point.
- **Commands and Information**:
  - List all loaded worlds.
  - View detailed world information (environment, settings, spawn point, player count).
  - Reload the configuration to apply changes without restarting the server.
- **Configuration**:
  - YAML-based configuration with default settings for new worlds.
  - Ensures all worlds have a defined respawn world.

## Installation

1. **Download**: Obtain the JAR file from your source (e.g., developer or repository).
2. **Place in Plugins Folder**: Place `MultiWorldManager.jar` in your server's `plugins/` directory.
3. **Start Server**: Restart the server. The plugin will generate `config.yml` and `plugin.yml` if needed.
4. **Configure**: Edit `plugins/MultiWorldManager/config.yml` as needed.
5. **Reload**: Use `/world reload` to apply changes (requires `multiworld.admin` permission).

**Note**: On first enable, if `auto-import-worlds` is true, existing worlds will be imported into the configuration.

## Configuration

The configuration file is located at `plugins/MultiWorldManager/config.yml` and supports automatic saving and reloading.

### Key Options

- **auto-load-worlds**: (boolean, default: true) - Automatically load worlds from the configuration on startup or reload.

- **auto-import-worlds**: (boolean, default: true) - Import existing world folders (containing level.dat) into the configuration on first enable or reload.

- default-settings

  :

  - gamemode: (string, default: SURVIVAL) - Default gamemode for new or imported worlds.
  - pvp: (boolean, default: true) - Default PVP setting.
  - difficulty: (string, default: NORMAL) - Default difficulty.

- worlds

  : Per-world configuration section, each world includes:

  - environment: (string, e.g., NORMAL, NETHER, THE_END)
  - type: (string, e.g., NORMAL, FLAT, LARGE_BIOMES, AMPLIFIED)
  - spawn: (sub-section with x, y, z, yaw, pitch) - Custom spawn point.
  - respawn-world: (string) - World to respawn in if dying here (without bed/anchor).
  - gamemode: (string, e.g., SURVIVAL, CREATIVE)
  - pvp: (boolean)
  - difficulty: (string, e.g., PEACEFUL, EASY, NORMAL, HARD)

- **default-respawn-world**: (string, default: world) - Fallback respawn world if a world's respawn-world is missing or invalid.

### Example Configuration

```yaml
auto-load-worlds: true
auto-import-worlds: true
default-settings:
  gamemode: SURVIVAL
  pvp: true
  difficulty: NORMAL
worlds:
  world:
    environment: NORMAL
    type: NORMAL
    spawn:
      x: 0.0
      y: 64.0
      z: 0.0
      yaw: 0.0
      pitch: 0.0
    respawn-world: world
    gamemode: SURVIVAL
    pvp: true
    difficulty: NORMAL
  nether_world:
    environment: NETHER
    type: NORMAL
    respawn-world: world
    # ... other settings
default-respawn-world: world
```

Apply changes via `/world reload`. New or imported worlds inherit default settings.

## Commands

All commands require the `multiworld.use` permission by default unless specified. Main command: `/world <subcommand> [args]`. No aliases.

- **/world reload**
  Permission: multiworld.admin
  Reloads the configuration, imports worlds (if enabled), loads worlds, and applies settings to all loaded worlds and online players.
  Usage: `/world reload`
- **/world import** 
  Manually imports an existing world folder (must contain level.dat) into the configuration and loads it.
  Usage: `/world import myworld`
- **/world create  [environment] [type]**
  Creates a new world with optional environment (default: NORMAL) and type (default: NORMAL). Adds to the configuration.
  Usage: `/world create mynether NETHER FLAT`
- **/world tp** 
  Teleports the player to the specified world's spawn point.
  Usage: `/world tp world_nether` (player-only)
- **/world list**
  Lists all loaded worlds.
  Usage: `/world list`
- **/world info [world]**
  Shows detailed information for a world (or current world if omitted): environment, gamemode, PVP, difficulty, respawn world, spawn coordinates, player count.
  Usage: `/world info world`
- **/world setspawn**
  Sets the current world's spawn to the player's current location (player-only). For coordinates, use the separate `/setspawn` command.
  Usage: `/world setspawn`
- **/world setrespawn**  
  Sets the respawn world for the specified world.
  Usage: `/world setrespawn myworld world`
- **/world setgamemode**  
  Sets the gamemode for a world (SURVIVAL, CREATIVE, ADVENTURE, SPECTATOR).
  Usage: `/world setgamemode myworld CREATIVE`
- **/world setpvp  <true/false>**
  Enables/disables PVP in a world.
  Usage: `/world setpvp myworld false`
- **/world setdifficulty**  
  Sets the difficulty (PEACEFUL, EASY, NORMAL, HARD).
  Usage: `/world setdifficulty myworld HARD`
- **/setspawn []    [yaw] [pitch]**
  Permission: multiworld.admin
  Sets the spawn point for a world using coordinates. If no world is specified, uses the current world. Console must specify the world.
  Usage (player): `/setspawn 0 64 0` or `/setspawn myworld 100 70 200 90 0`
  Usage (console): `/setspawn world 0 64 0`

Tab completion is supported for subcommands, world names, environments, types, gamemodes, etc.

## Permissions

- **multiworld.use** (default: op) - Allows use of basic commands like list, info, tp.
- **multiworld.admin** (default: op) - Allows admin commands: reload, import, create, setspawn, setrespawn, setgamemode, setpvp, setdifficulty.
- **multiworld.bypass.gamemode** (default: op) - Bypasses gamemode enforcement on world change/join.

Assign via permission plugins (e.g., LuckPerms).

## Listeners and Events

- **World Change/Join**: Applies the world's gamemode to players (unless bypassed). Sends a message if changed.
- **Player Respawn**: If no bed/respawn anchor, respawns in the configured respawn world at its spawn point. Attempts to load the world if unloaded; falls back to the first world if invalid.

These ensure a consistent per-world experience.

## FAQ

- **Why isn't my world loading?** Check the configuration for `auto-load-worlds: true` and valid environment/type. Use `/world reload`.
- **How do I unload a world?** Not directly supported; use Bukkit's unloadWorld() via other plugins or console.
- **How do I delete worlds?** Manually remove folders and configuration entries, then reload.
- **Respawn not working?** Ensure the respawn world is defined and loadable. Beds/respawn anchors take priority.
- **Performance?** Lightweight; only loads worlds as needed.
- **Compatible with Multiverse?** May conflict; test in a development environment.
