# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A single Docker image definition (`postgres/`) that builds a PostgreSQL database preloaded with the dialogue data — actors, conversations, dialogues and links — extracted from the game Disco Elysium (ZA/UM). There is no application code here: no server, no client, no build pipeline. The repo is just `postgres/Dockerfile` + `postgres/dump.sql` + `README.md`.

`postgres/dump.sql` is a `pg_dump` output produced once by loading the game's exported MySQL tables into a throwaway MySQL container, migrating them to a throwaway Postgres container with [pgloader](https://github.com/dimitri/pgloader), then dumping the result. There is no source/build step for `dump.sql` in this repo — it is a static, committed artifact. If it ever needs regenerating from a new export, that MySQL→pgloader→pg_dump pipeline has to be repeated from scratch; there's no script for it checked in.

## Commands

Build and run the database:

```bash
docker build -t disco-postgres ./postgres
docker run -d --name disco-postgres -p 5432:5432 disco-postgres
```

Defaults baked into the Dockerfile: db `disco`, user `disco`, password `disco` — override with `-e POSTGRES_DB=... -e POSTGRES_USER=... -e POSTGRES_PASSWORD=...` on `docker run`.

Query it:

```bash
docker exec -it disco-postgres psql -U disco -d disco
```

## Schema

All tables live in the `disco` schema (not `public`) — queries need `disco.actors`, `disco.dialogues`, etc., or `SET search_path TO disco, public;` first.

- `disco.actors` (`actorid`, `name`, `isnpc`, `isplayer`, `color`) — characters/skills/objects that can "speak" a line.
- `disco.conversations` (`conversationid`, `articyid`, `title`, `description`, `actorid`, `conversantid`) — top-level dialogue trees; `actorid`/`conversantid` reference `actors.actorid`.
- `disco.dialogues` (`dialogueid`, `title`, `text`, `articyid`, `actorid`, `entrytype`, `conversationid`, `voiceline`) — individual lines/nodes within a conversation; `conversationid` references `conversations.conversationid`, `actorid` references `actors.actorid`. `entrytype` distinguishes structural node kinds (e.g. `Fork`, `DialogueFragment`, `Instruction`) from plain lines.
- `disco.links` (`origindialogue`, `destinationdialogue`, `isconnector`, `conversationid`, `outputconversationid`, `originindex`) — edges connecting `dialogues` rows into a graph within/across conversations.

None of these tables have primary or foreign keys defined (the source data doesn't have unique keys to hang them off), so joins between tables are by convention on the `*id` columns above, not enforced by the schema. `disco.dialogues.text` has a GIN full-text index (`to_tsvector('simple', text)`) for search. All the `*id` join columns (`actors.actorid`, `conversations.conversationid`, `dialogues.dialogueid`/`conversationid`, `links.origindialogue`/`destinationdialogue`/`conversationid`/`outputconversationid`) are btree-indexed too — `actorid` and `conversationid` are unique, `dialogueid` is not (~1.7k duplicate `dialogueid` values in `dialogues`). Note `conversations.actorid`/`conversantid` are stored as `text` while `actors.actorid` is `bigint`, so that particular join still relies on an implicit cast rather than hitting the index directly — fine at this table's size (~1.5k rows), but worth knowing if it ever gets slow.

## Provenance and rights

The dialogue data is extracted from Disco Elysium and belongs to ZA/UM — see `README.md` for the full disclaimer. This repo doesn't own or relicense that content; don't add a LICENSE file implying otherwise.
