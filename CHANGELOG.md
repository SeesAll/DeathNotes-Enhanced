# Changelog

All notable changes to Death Notes Enhanced are documented here.

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
