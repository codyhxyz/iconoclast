# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.4.0] — 2026-04-18

### Changed
- Renamed from `iconoclast` to `eureka`. Same agent, new name.
- Plugin manifest, single-plugin marketplace manifest, agent file, slash command (`/eureka`), install URLs, and example invocations updated.

### Breaking
- Users on `0.3.0` will need to reinstall under the new name and remove the old agent file from `~/.claude/agents/iconoclast.md`.

## [0.3.0] — 2026-04-17

### Changed
- Renamed from `go-ham` to `iconoclast`. Same agent, more gravitas. The name now names the disposition: one who smashes the received answer.
- All manifests, the agent file, the `/iconoclast` slash command, install URLs, and example invocations updated.

### Breaking
- Users on `0.2.0` (`go-ham`) will need to reinstall under the new name and remove the old agent file from `~/.claude/agents/go-ham.md`.

## [0.2.0] — 2026-04-17

### Changed
- Renamed plugin and agent from `experimental-engineer` to `go-ham`. The name now doubles as the command you're giving Claude: *go ham*.
- Plugin manifest, marketplace manifest, agent file, and slash command (`/go-ham`) updated to match.
- Install commands and raw-file URLs now point at `codyhxyz/go-ham`.

### Breaking
- Users on `0.1.0` will need to reinstall under the new name. The old `experimental-engineer` agent file can be removed from `~/.claude/agents/`.

## [0.1.0] — 2026-04-16

### Added
- Initial release of the `experimental-engineer` subagent.
- Claude Code plugin manifest (`.claude-plugin/plugin.json`).
- Single-plugin marketplace manifest (`.claude-plugin/marketplace.json`) so the repo can be installed directly via `/plugin marketplace add`.
