# Changelog

All notable changes to Death Notes Enhanced are documented here.

## 1.1.1 - 2026-10-12

- Discord messages now use each webhook's configured name by default instead of overriding it with `Death Notes Enhanced`.
- Existing configurations using the former untouched username default migrate automatically to an empty override.
- Custom webhook username overrides remain supported through the `Username` setting, which is equivalent to DeathMessage's `Bot Name`.

## 1.1.0 - 2026-10-12

- Added built-in English, French, Russian, German, and Ukrainian localization.
- Death messages now use each recipient's Oxide language preference; console and Discord use the server language.
- Localized death messages are cached once per language and distance-unit combination for each death event.
- Added localized distance-unit labels and `/dn` command responses.
- Added placeholder validation so a malformed customized translation safely falls back to the configured English template.
- Kept custom death-message templates intact; templates that differ from the shipped defaults remain server-authored text in every language.
- Changed the default chat icon to the ScavengersHaven Steam account (`76561199568857188`) and migrates the previous untouched default automatically.

## 1.0.1 - 2026-10-12

- Removed the `[DeathNotes]` prefix from the default in-game chat format.
- Simplified default player-versus-player and player-versus-NPC messages to: `{killer} killed {victim} with {weapon} over a distance of {distance}.`
- Changed the default highlight color for names, weapons, attachments, and distance to warm yellow (`#FFC107`) while keeping surrounding text and unrelated variables grey (`#838383`).
- Added idempotent config merging for missing format/color entries and rule-key deduplication that preserves the first configured rule.
- Added a conservative migration for unchanged 1.0.0 presentation defaults while preserving administrator-customized messages and colors.

## 1.0.0 - 2026-10-12

- Forked the maintained Death Notes 6.5.8 codebase under its MIT license.
- Added participant, Rust-team, and Clans recipient targeting with deduplication.
- Enabled both Rust Teams and Clans integration by default; neither is a hard dependency.
- Added direct Discord webhook delivery with a bounded queue, 429/5xx retry handling, rich-text stripping, mention suppression, and a 2,000-character limit.
- Added per-rule `Show Kills in Discord` settings and optional Discord output for Patrol Helicopter and Bradley first-tag notices.
- Added `/dn testdiscord` and `deathnotes.testdiscord` test commands.
- Added the `deathnotes.admin` permission.
- Changed first-tag notices for Patrol Helicopter and Bradley to disabled by default.
- Changed default death categories to enable PvP, PvP-adjacent, environmental, boss, vehicle, and world-entity deaths while leaving animal and NPC rules disabled.
- Added explicit player-versus-player blunt, stab, and explosion rules.
- Added environmental poison, radiation exposure, cold exposure, decay, electric shock, and generic rules.
- Added player-owned fire and external-wall rules.
- Replaced repeated linear rule scans with an indexed lookup preserving the original wildcard priority.
- Corrected killer-type fallback when `lastAttacker` is unavailable.
- Fixed inherited attack-history updates that were lost because the tracked value is a struct.
- Cleans stale tracked player attacks when a player disconnects, respawns, recovers, or is revived.
- Batches dynamically learned translation saves instead of writing the configuration repeatedly during busy combat.
- Hardened malformed and partially missing configurations with safe defaults and normalized permission sets.
- Fixed per-player distance units for Notify/UINotify and prevented one recipient's unit choice from leaking into another recipient's message.
- Hardened Discord queue saturation and unload behavior so an in-flight request cannot corrupt or double-dequeue the queue.
- Escaped player-controlled display names before inserting them into Rust rich text.
- Avoided duplicate flame-tracking components and unnecessary audience/Clans lookups when no in-game output is enabled.
- Normalized custom permission registration.
- Added compatibility-friendly animal detection for Rust updates that introduce new animal classes.
- Added full setup, configuration, command, permission, webhook, color, integration, and developer documentation.
