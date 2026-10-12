# Death Notes Enhanced

Death Notes Enhanced is a Rust/Oxide plugin that creates detailed, configurable death notifications. It is based on the MIT-licensed Death Notes plugin and adds private participant/team/clan delivery, a dependency-free Discord webhook, safer defaults, and lower-overhead message matching.

## Highlights

- Sends in-game death messages only to the victim, killer/entity owner, and their online Rust team and clan members by default.
- Integrates directly with the popular `Clans` plugin through its public API. Clans is optional; the plugin continues working if it is absent.
- Posts directly to Discord without DiscordMessages, DiscordCore, or another Discord plugin.
- Queues webhook messages and handles Discord rate limits and temporary server errors with bounded retries.
- Supports chat, server console, Discord, Notify, and UINotify independently for every death-message rule.
- Keeps every message, color, name, body part, weapon, attachment, distance unit, and output configurable.
- Uses an indexed rule lookup instead of repeatedly scanning the complete rule list for every death.
- Retains the `OnDeathNotice` compatibility hook used by other plugins.

## Installation

1. Remove or unload another death-message plugin if it would produce duplicate notices.
2. Place `DeathNotes.cs` in `oxide/plugins`.
3. Let Oxide compile the plugin and create `oxide/config/DeathNotes.json`.
4. Edit the generated configuration as needed, then reload with `oxide.reload DeathNotes`.

No plugin dependency is required. `Clans`, `Notify`, and `UINotify` are optional integrations.

## Default behavior

In-game chat is restricted to the involved players and their communities:

- Killer, or the owner of the killing trap/defense
- Victim
- Online members of the killer's and victim's Rust teams
- Online members of the killer's and victim's Clans

Recipients are deduplicated. Clan allies are not included. If `Clans` is absent, direct participants and Rust teams still work.

Chat, server-console, and Discord outputs are enabled by default. Discord makes no request and prints no warning while its webhook URL is empty. Console and Discord are server-wide records; the participant/team/clan restriction applies to in-game chat, Notify, and UINotify.

Enabled rule groups by default:

- All direct PvP damage, including bullets, arrows, melee, explosives, fire, bleeding, collisions, and fallback damage
- PvP-adjacent deaths from player-owned traps, turrets, barricades, walls, fire, locks, SAM sites, and reflected damage
- Suicide, falling, drowning, hunger, thirst, cold, radiation, heat, poison, electric shock, decay, and generic environmental damage
- Bradley APC and Patrol Helicopter deaths and destruction
- SAM-site deaths
- Vehicle crashes, trains, Cargo Ship, Tugboat, elevators, and other supported world entities

Animal, shark, bee, NPC, scientist, and NPC-versus-NPC rules are included but disabled by default. First-tag notices for Patrol Helicopter and Bradley are also disabled by default. Set a rule's `Enabled` value or the tag-output toggles to enable them.

## Discord webhook

Configure the generated `Discord Webhook` section:

```json
"Discord Webhook": {
  "Enabled": true,
  "Webhook URL": "https://discord.com/api/webhooks/WEBHOOK_ID/WEBHOOK_TOKEN",
  "Username": "Death Notes Enhanced",
  "Avatar URL": "",
  "Message Format": "{message}",
  "Maximum Queued Messages": 100,
  "Maximum Retries": 3
}
```

The webhook URL is never written to plugin logs. Discord output strips Rust rich-text tags, prevents user/role mentions, enforces Discord's 2,000-character content limit, and retries HTTP 429, 5xx, and connection failures.

Run `deathnotes.testdiscord` in the server console or `/dn testdiscord` as an authorized administrator to send a test message.

Do not commit a real webhook URL to a public repository.

## Audience configuration

```json
"Audience": {
  "Restrict In-Game Recipients": true,
  "Include Killer or Entity Owner": true,
  "Include Victim": true,
  "Include Rust Teams": true,
  "Include Clans Plugin Members": true
}
```

Set `Restrict In-Game Recipients` to `false` for traditional global chat broadcasts. Rust Teams and Clans can be enabled together safely; all recipients are combined in a set before delivery.

## Message rules

Each entry under `Translations` -> `Death Messages` independently controls matching and output:

```json
{
  "KillerType": "Player",
  "VictimType": "Player",
  "DamageType": "Bullet",
  "Enabled": true,
  "Display Permissions (Display For All Players If Empty)": [],
  "Show Kills in Console": true,
  "Show Kills in Chat": true,
  "Show Kills in Discord": true,
  "Show Kills in Notify": false,
  "Show Kills in UINotify": false,
  "Messages": [
    "{killer} shot {victim} using their {weapon} over a distance of {distance}."
  ]
}
```

Only one rule should use a given killer/victim/damage combination. `*` matches any value and `-` matches the absence of a value. Matching preference is exact, followed by progressively broader wildcard rules.

Supported killer/victim types include:

`Helicopter`, `Bradley`, `Animal`, `Murderer`, `Scientist`, `Player`, `Trap`, `Turret`, `Barricade`, `ExternalWall`, `HeatSource`, `Fire`, `Lock`, `Sentry`, `ScarecrowNPC`, `TunnelDweller`, `UnderwaterDweller`, `ZombieNPC`, `GingerbreadNPC`, `HeavyScientist`, `Minicopter`, `ScrapTransportHelicopter`, `AttackHelicopter`, `BeeSwarm`, `SAM`, `Shark`, `NPCShopKeeper`, `ExcavatorArm`, `Drone`, `CarShredder`, `Cactus`, `Train`, `MagnetCrane`, `Elevator`, `HotAirBalloon`, `BatteringRam`, `Tugboat`, `CargoShip`, `CustomNPC`, and `TravellingVendor`.

Supported damage types include:

`Generic`, `Hunger`, `Thirst`, `Cold`, `Drowned`, `Heat`, `Bleeding`, `Poison`, `Suicide`, `Bullet`, `Slash`, `Blunt`, `Fall`, `Radiation`, `Bite`, `Stab`, `Explosion`, `RadiationExposure`, `ColdExposure`, `Decay`, `ElectricShock`, `Arrow`, and `Collision`.

## Placeholders and colors

Always available:

- `{victim}` — victim name
- `{position}` — world coordinates
- `{mapgrid}` — map-grid location

Available when a killer exists:

- `{killer}` — killer/entity name
- `{bodypart}` — last hit body part
- `{distance}` — distance between killer and victim

Available for player/custom-NPC/drone kills:

- `{hp}` — killer's remaining health
- `{weapon}` — weapon name
- `{attachments}` — weapon attachments

Available for owned traps and defenses:

- `{owner}` — entity owner's player name

The default chat format is:

```json
"Chat Format": "<color=#838383>[<color=#80D000>DeathNotes</color>] {message}</color>"
```

Individual variable colors are configured under `Variable Colors`. This lets a server apply its own color scheme without editing the source. Discord receives plain text because Rust color tags are removed before delivery.

See [`examples/ScavengersHaven.config.example.json`](examples/ScavengersHaven.config.example.json) for a concise example. The plugin fills in missing default translations when it loads.

## Commands

Player chat commands:

- `/dn` — show available options
- `/dn enable` — receive eligible in-game notices
- `/dn disable` — hide in-game notices
- `/dn teamonly` — toggle participant/team/clan-only viewing when global delivery is configured
- `/dn meters` — use meters
- `/dn feet` — use feet
- `/dn yards` — use yards
- `/dn testdiscord` — send a webhook test; administrator permission required

Server-console command:

- `deathnotes.testdiscord` — send a webhook test

## Permissions

- `deathnotes.cansee` — required to receive notices when `Require Permission (deathnotes.cansee)` is enabled
- `deathnotes.cantsee` — blocks all in-game notices for the player
- `deathnotes.suppress` — prevents deaths involving that player from generating notices
- `deathnotes.seeteamonly` — limits that player to notices involving participants, Rust teams, or Clans
- `deathnotes.imperialfeet` — displays distances in feet
- `deathnotes.imperialyards` — displays distances in yards
- `deathnotes.admin` — permits `/dn testdiscord`

Additional permissions beginning with `deathnotes.` can be assigned to individual message rules.

## Optional integrations

- **Clans:** calls `GetClanMembers(ulong playerId)` for each involved player or entity owner.
- **Notify:** sends rules with `Show Kills in Notify` enabled.
- **UINotify:** sends rules with `Show Kills in UINotify` enabled.

None of these plugins is a hard dependency.

## Developer hook

```csharp
object OnDeathNotice(Dictionary<string, object> data, string message)
```

Return `false` to cancel the notice. The data dictionary contains `VictimEntityType`, `VictimEntity`, `KillerEntityType`, `KillerEntity`, `DamageType`, and `HitInfo`.

## Versioning and license

See [CHANGELOG.md](CHANGELOG.md) for releases. This project remains licensed under the MIT License and preserves the original Death Notes copyright notice in [LICENSE.md](LICENSE.md).
