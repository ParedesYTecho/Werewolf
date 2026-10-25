# Technical case: Werewolf

[Back to README](../README.en.md) · [Español](caso-tecnico.md)

Werewolf is a Discord bot for playing the social-deduction game Werewolf without a human narrator, with a web dashboard and a shared database. The source code is private. This page describes four real problems from the project, how they were handled, and what is still open. It publishes no usage figures.

## Architecture in brief

- **Bot:** Python and discord.py. Each game lives in the channel where it was opened. Game state (players, phase, votes) is kept apart from the commands that drive it and from the engine that advances it.
- **Roles as strategies:** each role is a class with its own night logic instead of a long chain of conditionals, so adding or changing a role does not require touching the game loop.
- **Data:** Supabase (PostgreSQL), with the bot and the dashboard reading and writing the same schema.
- **Dashboard:** Next.js 16 and React 19 with Discord sign-in.

## 1. Computed decks instead of fixed presets

A fixed role split works for eight players and breaks at twenty. The bot builds the deck from the player count and what each role contributes, applies rules that reject unplayable combinations, and reserves three center cards for the roles that use them. The cap is 20 players (23 cards including the center ones).

To avoid relying on intuition, a Monte Carlo simulation plays games with heuristic agents and measures how each side wins at each table size. It runs as part of every pull request's checks. It measures deck tendencies, not the quality of a game played by people.

## 2. Per-server isolation

Statistics, achievements and configuration are stored per server: the key of every statistics table includes the server identifier. Even so, a project review found an isolation bug: changing the anonymity setting in one server could alter games in progress in other servers.

The bug was first reproduced with a failing test and only then fixed. The setting now affects only the server that edits it and no longer de-anonymises a Ranked game already under way. The test stays as a regression guard.

## 3. A bot that mutes itself

The bot is invited without the Administrator permission, which has a side effect: to lock a channel during voting, the permission to send messages is removed for everyone, and the bot loses its ability to speak as well.

The fix is to grant the bot that permission in the channel before locking it. If Discord refuses, the lock is skipped: a game with an open channel is preferred to a game without a voice. This is a deliberate decision with its rationale recorded. The invite link and the dashboard share a single permission definition so they cannot drift apart.

## 4. Discord IDs and JavaScript

Discord identifiers exceed the largest integer JavaScript can represent exactly (2^53). Parsing them with plain JSON rounds them and points at the wrong server or user. The dashboard handles those values as strings from read to write, with reads and writes that preserve the exact value.

## Testing and continuous integration

Every change lands through a pull request. A GitHub Actions workflow checks syntax, imports the bot (which registers every command), runs the test suite and a Monte Carlo simulation, and passing is required to merge. Confirmed defects get a regression test.

What this does not prove: a green test run does not mean the game is fully tested. There are no tests against real Discord, the simulations use heuristic agents, and coverage is not measured. A full rebuild of the test strategy is a pending initiative.

## What is still open

- Game recovery after a restart stores serializable state, but not every mode's data survives. This is documented internally and unresolved.
- Some independent win conditions have descriptions that do not fully match their implementation. They are treated as a pending decision and are not changed without agreement.
- Part of the project's legacy test code is still waiting for review.
