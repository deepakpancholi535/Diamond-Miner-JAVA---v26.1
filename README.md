# Aternos Diamond Miner (Java Edition)

A locally-run Minecraft Java bot built with Mineflayer. It scans the loaded world data it receives for diamond ore, attempts to collect reachable ore, and uses a short 1×2 branch-mining tunnel when no nearby diamond is visible. Version 1.1 adds limited defensive combat and automatic diamond storage in a chest you select in-game. This build is configured for direct connections to Minecraft Java 26.1 (without a protocol proxy).

This is a rule-based autonomous bot, not a large-language-model agent. It does **not** use an X-ray resource pack or read hidden server files. It can only detect ore represented as diamond ore in world data received from the server. Server anti-X-ray can hide or disguise ore.

## Direct connection (Minecraft Java 26.1)

This release is intended for direct connections to Java servers running Minecraft 26.1. It does not bundle, launch, or configure ViaProxy. Make sure the server is actually running 26.1 and that Mineflayer supports the version.

1. Start your server and open its **Connect** page.
2. Copy the address and port.
3. In `config.json`, set `server.host` to the hostname only and `server.port` to the numeric port.
4. Set `server.version` to `26.1`.
5. Run `start.bat`.

For offline/cracked servers, this assumes the server allows offline players. Use Microsoft authentication on servers that require it.

## Configuration

Use the current server hostname and port from the server's connection panel. Avoid publishing your server address, username, or personal configuration in the repository.

## Features

- Connects to a Java server using Microsoft authentication, or offline authentication only when the server explicitly allows offline/cracked players.
- Detects `diamond_ore` and `deepslate_diamond_ore` in loaded chunks within the configured scan radius.
- Uses pathfinding and the collect-block plugin to approach, mine, and pick up detected ore.
- Uses a configurable 1×2 branch-mining tunnel when no nearby visible ore is found.
- Defends itself from a curated list of common hostile mobs using a sword or axe and the `mineflayer-pvp` plugin.
- Refuses melee fights with creepers, wardens, withers, ender dragons, ravagers, elder guardians, ghasts, and endermen; it stops mining and attempts to return to its saved starting position instead.
- Stops combat when health gets too low. Combat is limited by a time and detection radius so the bot does not chase mobs indefinitely.
- Saves a chest you point at with `!setchest`, deposits diamonds into it automatically at the configured threshold, and returns to the position where it started the deposit.
- Deposits remaining diamonds when a mining session ends, provided automatic storage is enabled and a chest has been saved.
- Eats common food items from its inventory when hunger drops.
- Avoids liquids/hazards conservatively and stops for low health, low food, a nearly full inventory, missing iron-tier pickaxe, failed ore targets, or its tunnel-length limit.
- Does not auto-reconnect, start/restart the server, interact with the Aternos website, or attempt to keep an empty server online.

## Requirements

- Windows 10/11 (included `start.bat`) or another OS with Node.js and npm.
- Node.js 20 or later; Node.js 24 LTS is a good choice.
- A **second** Minecraft Java account if the server requires Microsoft authentication. The account must own Minecraft Java Edition. Do not use the same account you are already using to play; Minecraft will not allow the same account to join twice at once.
- An iron, diamond, or netherite pickaxe to mine diamond ore.
- A sword or axe so the bot can fight the supported hostile mobs.
- Food and armor are strongly recommended. The bot is not guaranteed to survive combat or every hazard.
- Permission from the server owner to run a bot.

## Windows setup

1. Install the LTS version of Node.js from <https://nodejs.org/>. Close and reopen Command Prompt after installation.
2. Extract this ZIP to a normal folder, for example `C:\Minecraft\diamond-miner-agent`.
3. Double-click `start.bat` once. It creates `config.json` from `config.example.json` and exits.
4. Open `config.json`. The Aternos host and port are already filled in. Set:
   - `server.auth`: `microsoft` if the server requires a premium Microsoft account. Use `offline` only if the Aternos **Cracked** option is enabled and offline players are allowed.
   - `server.username`: for Microsoft auth, the email of a separate Microsoft account that owns Minecraft Java Edition. Mineflayer will guide you through Microsoft sign-in. For offline auth, use the bot's exact in-game name.
   - `owner`: your exact in-game Minecraft username. Only this player can issue bot commands.
5. If the server whitelist is enabled, add the bot's actual in-game account/name to the whitelist.
6. Start the Aternos server yourself and wait until it shows **Online**.
7. Double-click `start.bat` again. It installs npm packages the first time and starts the bot. Complete the Microsoft sign-in/device-code flow in the terminal if prompted.
8. Join the server yourself. The bot should join and wait for commands. Do not use it to keep an otherwise-empty Aternos server online.

## One-time in-game setup

1. Place a normal chest or trapped chest at your mining base. Make sure the bot can walk to it and open it. Leave space in the chest.
2. Move the bot to within 6 blocks of the intended chest. You can stand next to it and use `!come` to bring the bot close.
3. Type `!setchest` in chat. The bot finds the nearest normal/trapped chest within 6 blocks and saves its coordinates into `config.json` on your PC. If multiple chests are close together, place the bot closest to the exact chest you want it to use.
4. Give the bot an iron-or-better pickaxe, a sword or axe, armor if possible, and plenty of food. Drop the items near it and type `!pickup`.
5. Move the bot to a safe, already-open 1×2 mining corridor around Y=-58, with a solid floor. The bot's `!mine` command captures this starting location as its return position.
6. Type `!mine` and watch the first session closely.

## Chat commands

| Command | Action |
|---|---|
| `!help` | Show commands |
| `!pickup` | Collect dropped equipment/food within 8 blocks |
| `!setchest` | Save the nearest chest within 6 blocks of the bot |
| `!chest` | Show saved chest coordinates and auto-deposit setting |
| `!deposit` | Deposit all diamonds currently in inventory |
| `!come` | Walk toward the configured owner and save that position |
| `!mine` | Start mining; current position becomes the return point |
| `!stop` | Stop after/cancel the current action, deposit remaining diamonds if configured, then stop |
| `!home` | Stop, deposit remaining diamonds if configured, then return to the mining start position |
| `!status` | Show health, food, diamonds, saved chest, mobs defeated, and mining progress |

Commands are accepted only from the exact player name in `owner`.

## Combat behavior and limits

- The bot detects supported hostile mobs within `combat.detectRadius` while mining. It interrupts collection/pathfinding and switches to a melee weapon before engaging.
- Supported melee targets include zombies, skeleton variants, spiders, silverfish, endermites, witches, pillagers, vindicators, evokers, vexes, slimes, magma cubes, phantoms, blazes, hoglins, zoglins, piglin brutes, guardians, and breezes.
- It deliberately does not melee creepers, wardens, withers, ender dragons, ravagers, elder guardians, ghasts, or endermen. It stops mining and attempts to go home when one of these is detected.
- If health reaches `combat.retreatHealth`, combat stops and the bot attempts to return to its saved mining start position.
- The bot will not attack players. It may still lose fights, get knocked into hazards, or fail to path out of a cave. Bring armor, a shield, a strong weapon, and food; keep an eye on it.

## Diamond chest storage

- `!setchest` is required once. Move the bot within 6 blocks of a normal chest/trapped chest; it saves the nearest one.
- `storage.autoDepositDiamonds` enables automatic storage.
- `storage.depositAtCount` defaults to `16`: after the bot has collected at least 16 diamonds it travels to the saved chest, deposits diamonds, and returns to the position where it started the deposit.
- If a mining session ends (`!stop`, `!home`, tunnel limit, or a safety stop), the bot deposits any remaining diamonds if auto-storage is enabled and a chest has been saved.
- `!deposit` forces an immediate deposit of all diamonds in its inventory. It only deposits diamonds; it does not empty other inventory items.
- If the chest is full, missing, blocked, or unreachable, the bot reports the problem in chat/terminal. It will not discard diamonds.

## Configuration

Relevant settings in `config.json`:

```json
{
  "server": {
    "host": "xyz.aternos.me",
    "port": 20365,
    "auth": "microsoft",
    "username": "YOUR_MICROSOFT_ACCOUNT_EMAIL"
  },
  "owner": "YOUR_MINECRAFT_USERNAME",
  "combat": {
    "enabled": true,
    "detectRadius": 9,
    "maxFightSeconds": 18,
    "retreatHealth": 8
  },
  "storage": {
    "autoDepositDiamonds": true,
    "depositAtCount": 16,
    "chestPosition": null,
    "returnAfterDeposit": true
  }
}
```

Keep `config.json` private. Do not share Microsoft passwords, device codes, or authentication tokens. Mineflayer may create local authentication cache files; keep those private too.

## Aternos rules

Aternos says servers automatically stop when the last player leaves and prohibits using bots/scripts to bypass this behavior. Use this bot only during a real play session in which you or other real players are actively playing. Do not use it as an AFK/keep-alive bot, do not automatically reconnect after disconnection, and do not use it to keep an otherwise-empty server online. See <https://support.aternos.org/hc/en-us/articles/31771896948253-24-7-Hosting> and <https://aternos.gmbh/en/aternos/terms>.

## Important limitations / troubleshooting

- The bot sees only loaded world data received from the server. It cannot identify diamonds in unexplored/unloaded chunks, and anti-X-ray may disguise ores. A resource pack cannot bypass server-side anti-X-ray.
- The bot has not been tested against your live Aternos world. Watch its first sessions and back up the world before testing automation.
- If the server address/port changes, update `config.json` from the Aternos **Connect** panel.
- If authentication fails, verify whether the server requires Microsoft auth or permits offline mode. Never enter your Microsoft password in `config.json` or chat.
- If it cannot deposit diamonds, check that the chest still exists at the saved coordinates, is not blocked, and has free slots. Aim at the correct chest and run `!setchest` again if needed.
- If it will not fight, give it a sword or axe and enough health/armor. It intentionally avoids certain especially dangerous mobs.
- If Mineflayer reports an unsupported protocol, use a server version supported by the installed Mineflayer release or update dependencies when compatible.
- `!home` and chest return are best-effort navigation actions. They can fail if the route is blocked, chunks are not loaded, or the bot is trapped.


