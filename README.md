# dTournaments

> A fully-featured, highly configurable tournament plugin for **Paper 26.1+**

[![Paper](https://img.shields.io/badge/Paper-1.21.6+-orange?logo=data:image/png;base64,)](https://papermc.io)
[![Java](https://img.shields.io/badge/Java-21+-blue?logo=openjdk)](https://adoptium.net)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

dTournaments lets you run live, automated tournaments on your server with minimal setup. Players can break blocks, catch fish, kill mobs, gain XP in third-party skill plugins, and dozens of other activities - competing for command and item rewards, tracked in real-time on a live scoreboard.

---

## Feature Highlights

- **Two tournament modes** - *Regular* (highest score wins) and *Challenge* (first to reach a goal)
- **19 built-in objectives** - block breaking/placing, mob/player kills, boss kills, fishing, crafting, enchanting, taming, breeding, jumping, distance, sneaking, shooting, villager trades, item pickup, XP gain, level-up, and more
- **6 plugin integrations** - mcMMO, Jobs Reborn, MythicMobs, AuraSkills, CrazyCrates *(all optional)*
- **Tournament Pools** - group multiple tournaments under one schedule; run them randomly or in round-robin rotation
- **Flexible scheduling** - Hourly, Daily, Weekly, Monthly, or a specific date and time, with configurable timezone
- **Persistent live scoreboard** - smartly yields to minigame scoreboards and restores itself automatically
- **Interactive GUI** - live-updating tournament browser with top-10 leaderboard, timer, stats, and pre-join support
- **Boss bars** - countdown progress bar during tournaments, upcoming-tournament notification bar
- **Sounds** - 3-2-1 countdown beeps on start, fanfare on end
- **Rewards** - command rewards per-place in config, or an in-game GUI item reward editor
- **CoreProtect integration** - prevents block-break farming by ignoring player-placed blocks
- **SQLite & MySQL** - with a built-in migration command to move between them
- **Full PlaceholderAPI support** - 30+ placeholders for scoreboards, holograms, and more
- **MiniMessage + legacy color code support** - full RGB and gradient support everywhere
- **Multi-language** - ships with `en_us` and `cs_cz`, hot-swappable at runtime
- **Developer API** - register custom objectives and query active tournament data from your own plugin

---

## Requirements

| Requirement | Version |
|---|---|
| [Paper](https://papermc.io/downloads/paper) (or fork) | 26.1+ |
| Java | 21+ |

**Optional soft dependencies** (enable extra objectives/features):

| Plugin | Feature unlocked |
|---|---|
| [Vault](https://github.com/MilkBowl/Vault) | Entry fees, economy rewards |
| [PlaceholderAPI](https://www.spigotmc.org/resources/placeholderapi.6245/) | 30+ placeholders |
| [CoreProtect](https://www.spigotmc.org/resources/coreprotect.8631/) | Anti-farm for block break tournaments |
| [mcMMO](https://www.spigotmc.org/resources/official-mcmmo-original-author-returns.64348/) | `mcmmo_xp` objective |
| [Jobs Reborn](https://www.spigotmc.org/resources/jobs-reborn.4216/) | `jobs_xp` objective |
| [MythicMobs](https://www.spigotmc.org/resources/mythicmobs.5702/) | `mythicmob_kill` objective |
| [AuraSkills](https://www.spigotmc.org/resources/auraskills.81069/) | `auraskills_xp` objective |
| [CrazyCrates](https://www.spigotmc.org/resources/crazycrates.17743/) | `crazy_crates_open` objective |

---

## Quick Start

1. Drop the `dTournaments.jar` into your `plugins/` folder.
2. Start the server. The plugin generates `plugins/dTournaments/config.yml` and an example tournament at `plugins/dTournaments/tournaments/example.yml`.
3. Edit `example.yml` (or copy it to create more) to set up your first tournament:
   ```yaml
   display-name: "<gold>Daily Mining Race"
   description: "Mine the most blocks in one hour!"
   type: REGULAR
   objective: block_break
   duration: 3600
   participation: AUTO
   schedule:
     enabled: true
     type: DAILY
     time: "20:00"
   rewards:
     - place: 1
       commands:
         - "eco give {player} 1000"
   ```
4. Reload the plugin with `/dt reload`.
5. Test it manually with `/dt start example`.

---

## Tournament Types

### Regular
The player with the **highest score** when the timer expires wins. Perfect for competitive events like mining races or fishing contests.

### Challenge
Players race to be the first to **reach a set goal**. You can configure how many players can complete it before the tournament closes, making it work for both first-past-the-post and top-N completion formats.

---

## Objectives

### Built-in

| Objective ID | Description | Options |
|---|---|---|
| `block_break` | Break blocks | `block-filter: []` - restrict to specific materials |
| `block_place` | Place blocks | `block-filter: []` |
| `mob_kill` | Kill vanilla mobs | `mob-filter: []` - restrict to specific entity types |
| `player_kill` | Kill other players | - |
| `kill_boss` | Kill wither/ender dragon/elder guardian | `boss-filter: []` |
| `damage_dealt` | Deal damage to any entity | - |
| `jump` | Jump | - |
| `distance` | Travel distance (per block) | - |
| `sneak` | Toggle sneak | - |
| `shoot` | Hit entities with a bow | - |
| `fish` | Catch fish | - |
| `craft` | Craft items | `item-filter: []` |
| `enchant` | Enchant items | - |
| `tame` | Tame animals | - |
| `breed` | Breed animals | - |
| `villager_trade` | Trade with villagers | - |
| `item_pickup` | Pick up items | `item-filter: []` |
| `xp_gain` | Gain vanilla XP | - |
| `level_up` | Level up (vanilla) | - |

### External (require the respective plugin)

| Objective ID | Plugin | Options |
|---|---|---|
| `mcmmo_xp` | mcMMO | `skill: MINING` (or `ANY`) |
| `jobs_xp` | Jobs Reborn | `job: Miner` (or `ANY`) |
| `mythicmob_kill` | MythicMobs | `mob-filter: []` |
| `auraskills_xp` | AuraSkills | `skill: farming` (or `ANY`) |
| `crazy_crates_open` | CrazyCrates | `crate-filter: []` |

---

## Tournament Pools

Pools let you schedule a single slot that picks from a set of tournaments - useful for variety servers that want a "Daily Tournament" that rotates through different activities.

```yaml
# plugins/dTournaments/pools/daily_variety.yml
display-name: "<gold>Daily Tournament"
mode: RANDOM        # RANDOM or ROTATION (round-robin)
members:
  - daily_mining
  - daily_fishing
  - daily_pvp
schedule:
  enabled: true
  type: DAILY
  time: "20:00"
```

Rotation indices survive server restarts - the plugin persists them to the database.

---

## Scheduling

Each tournament (or pool) can have its own schedule:

| Type | Example | Notes |
|---|---|---|
| `HOURLY` | Every 5 hours | Set `interval: 5` |
| `DAILY` | Every day at 20:00 | `time: "20:00"` |
| `WEEKLY` | Every Monday | `day: MONDAY` |
| `MONTHLY` | First of each month | `day-of-month: 1` |
| `SPECIFIC` | One-off event | `time: "2025-12-31 23:00"` |

The timezone is configured globally in `config.yml` (`timezone: system` follows the server's JVM timezone, or supply any valid Java ZoneId like `Europe/Prague`).

---

## Participation Modes

- **AUTO** - all online players are enrolled automatically when the tournament starts. Players who join mid-tournament are notified and added.
- **MANUAL** - players must click **Join** in the Tournaments GUI (`/dt`). Upcoming tournaments can be pre-joined in the GUI before they start.

Both modes support permission requirements and entry fees (requires Vault).

---

## Commands

Main command: `/dtournaments` (alias: `/dt`)

| Command | Permission | Description |
|---|---|---|
| `/dt` | `dtournaments.use` | Open the Tournaments GUI |
| `/dt help` | `dtournaments.use` | Show command help |
| `/dt start <id>` | `dtournaments.admin.start` | Force-start a tournament (bypasses min-players) |
| `/dt stop <id>` | `dtournaments.admin.stop` | Force-stop a running tournament |
| `/dt reload` | `dtournaments.admin.reload` | Reload all configs and language files |
| `/dt language <code>` | `dtournaments.admin.language` | Hot-swap the active language at runtime |
| `/dt rewards <id>` | `dtournaments.admin.rewards` | Open the item reward GUI for a tournament |
| `/dt update` | `dtournaments.admin.update` | Force-flush scores to the database |
| `/dt clear <id>` | `dtournaments.admin.clear` | Reset all scores in a running tournament |
| `/dt clearplayer <id> <player>` | `dtournaments.admin.clearplayer` | Reset a specific player's score |
| `/dt forcejoin <id> [player\|*]` | `dtournaments.admin.forcejoin` | Force-join a player (or everyone) into a tournament |
| `/dt pools` | `dtournaments.admin.reload` | List all pools and their next scheduled run |
| `/dt migrate` | `dtournaments.admin.migrate` | Migrate data from SQLite to MySQL |

**Player permissions:**

| Permission | Default | Description |
|---|---|---|
| `dtournaments.use` | everyone | Use `/dt` and view the GUI |
| `dtournaments.join` | everyone | Join tournaments manually via the GUI |
| `dtournaments.prejoin` | everyone | Pre-join upcoming tournaments via the GUI |

---

## 📊 PlaceholderAPI Placeholders

Requires [PlaceholderAPI](https://www.spigotmc.org/resources/placeholderapi.6245/) to be installed.

### Active tournament shortcuts
These resolve against the tournament expiring soonest (ideal for single-tournament servers).

| Placeholder | Returns |
|---|---|
| `%dtournaments_active_count%` | Number of running tournaments |
| `%dtournaments_active_name%` | Display name |
| `%dtournaments_active_type%` | `REGULAR` or `CHALLENGE` |
| `%dtournaments_active_time_remaining%` | Formatted time (e.g. `1h 23m 4s`) |
| `%dtournaments_active_time_remaining_seconds%` | Raw seconds |
| `%dtournaments_active_participants%` | Participant count |
| `%dtournaments_active_player_score%` | Requesting player's score |
| `%dtournaments_active_player_rank%` | Requesting player's rank |
| `%dtournaments_active_is_participating%` | `yes` / `no` |
| `%dtournaments_active_top1_name%` | 1st place player name |
| `%dtournaments_active_top1_score%` | 1st place score |
| `%dtournaments_active_top<N>_name%` | Nth place name |
| `%dtournaments_active_top<N>_score%` | Nth place score |

### Per-tournament (replace `<id>` with the tournament's config file name)

| Placeholder | Returns |
|---|---|
| `%dtournaments_is_active_<id>%` | `true` / `false` |
| `%dtournaments_name_<id>%` | Display name |
| `%dtournaments_type_<id>%` | Type |
| `%dtournaments_participants_<id>%` | Participant count |
| `%dtournaments_time_remaining_<id>%` | Formatted time remaining |
| `%dtournaments_time_remaining_seconds_<id>%` | Raw seconds remaining |
| `%dtournaments_player_score_<id>%` | Player's score |
| `%dtournaments_player_rank_<id>%` | Player's rank |
| `%dtournaments_is_participating_<id>%` | `yes` / `no` |
| `%dtournaments_top<N>_name_<id>%` | Nth place name |
| `%dtournaments_top<N>_score_<id>%` | Nth place score |

### All-time player stats *(async-cached, refreshes every 5 minutes)*

| Placeholder | Returns |
|---|---|
| `%dtournaments_alltime_wins%` | Total wins |
| `%dtournaments_alltime_participations%` | Total tournaments entered |
| `%dtournaments_alltime_score%` | Cumulative score across all tournaments |

---

## Rewards

Rewards run command strings when a tournament ends. The `{player}` placeholder is replaced with the winner's name.

```yaml
rewards:
  - place: 1
    commands:
      - "eco give {player} 1000"
      - "broadcast {player} won and earned $1000!"
  - place: 2
    commands:
      - "eco give {player} 500"
```

**Item rewards** can be configured through the in-game GUI with `/dt rewards <tournament-id>`. Items are stored separately and are awarded in addition to command rewards.

---

## Actions

Actions are triggered at key events and support multiple dispatch prefixes:

| Prefix | Example | Effect |
|---|---|---|
| `broadcast` | `broadcast <green>Tournament started!` | Server-wide broadcast |
| `msg <player>` | `msg {player} <green>You joined!` | Private message to a player |
| `console` | `console give {player} diamond 1` | Run command as console |
| `player-cmd <player>` | `player-cmd {player} home` | Run command as the player |

```yaml
actions:
  on-join:
    - "msg {player} <green>You joined the tournament!"
  on-start:
    - "broadcast <gold><bold>A tournament has started!"
  on-end:
    - "broadcast <gold>{winner} <yellow>won with {score} points!"
```

---

## Notifications

### Boss Bars
A progress boss bar is shown during tournaments and counts down as time runs out. A separate upcoming-tournament bar can appear before a tournament starts. Both are globally configurable and can be overridden per tournament.

### Sounds
- A 3-2-1 note-block countdown plays before a tournament begins
- A fanfare plays when a tournament ends

Both can be toggled in `config.yml`.

---

## Scoreboard

A live sidebar scoreboard shows tournament progress for participating players. Key behaviors:
- Updates on a configurable tick interval
- Shows tournament name, time remaining, your score, your rank, and top 3 players
- If multiple tournaments run simultaneously, the one expiring soonest takes priority
- **Smart displacement** - if some minigame applies its own scoreboard, the tournament scoreboard yields and restores itself when the minigame releases the player

The layout is fully configurable in `config.yml` under `scoreboard.layout`.

---

## Database

dTournaments stores all tournament history, scores, and player stats persistently.

| Option | Details |
|---|---|
| **SQLite** | Zero-config, default. Stored at `plugins/dTournaments/data.db` |
| **MySQL** | Set `storage.type: mysql` and fill in connection details in `config.yml`. Uses HikariCP connection pooling. |

To migrate from SQLite to MySQL, run `/dt migrate confirm` while SQLite is active, then switch `storage.type` to `mysql` and restart.

---

## Languages

All messages, tooltips, GUI text, and scoreboard lines are defined in language files located at `plugins/dTournaments/lang/`. The plugin ships with:
- `en_us.yml` - English
- `cs_cz.yml` - Czech

Switch languages without a restart: `/dt language cs_cz`

To add your own language, copy `en_us.yml`, translate it, and set `language: your_code` in `config.yml`.

---

## Developer API

Add dTournaments as a dependency in your project and interact with it through `DTournamentsAPI`:

```java
// Get the API instance after dTournaments has enabled
DTournamentsAPI api = DTournamentsAPI.get();

// Query active tournaments
Collection<Tournament> active = api.getActiveTournaments();

// Register a custom objective
api.registerObjective(new MyCustomObjective(plugin));
```

**Custom objectives** extend `AbstractInternalObjective` (or implement `TournamentObjective`) and fire score increments via `DTournaments.getInstance().getTournamentManager().incrementScore(uuid, objectiveId, delta)`.

---

## File Structure

```
plugins/dTournaments/
├── config.yml                  # Global settings (DB, timezone, scoreboard, etc.)
├── lang/
│   ├── en_us.yml               # English language file
│   └── cs_cz.yml               # Czech language file
├── tournaments/
│   ├── example.yml             # Example tournament - copy to create your own
│   └── my_tournament.yml       # Your custom tournament
├── pools/
│   └── example__1_.yml         # Example pool configuration
├── rewards/
│   └── my_tournament.yml       # Item rewards for a tournament (managed via GUI)
└── data.db                     # SQLite database (if using SQLite)
```

---

## Wiki

Full documentation is available on the [GitHub Wiki](../../wiki):

- [Installation & Requirements](../../wiki/Installation)
- [Tournament Configuration Reference](../../wiki/Tournament-Configuration)
- [Pool Configuration](../../wiki/Pools)
- [Objectives](../../wiki/Objectives)
- [Scheduling](../../wiki/Scheduling)
- [Rewards & Actions](../../wiki/Rewards-and-Actions)
- [Scoreboard](../../wiki/Scoreboard)
- [PlaceholderAPI](../../wiki/PlaceholderAPI)
- [Commands & Permissions](../../wiki/Commands-and-Permissions)
- [Database & Storage](../../wiki/Database)
- [Languages](../../wiki/Languages)
- [Developer API](../../wiki/Developer-API)

---

## Credits

Built by **DogeTennant** for Paper 26.1+.

Contributions, bug reports, and suggestions are welcome - open an issue or a pull request!
