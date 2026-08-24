<h1>Disco Elysium Explorer</h1>

<p>This repo builds a PostgreSQL database preloaded with the dialogue data (actors, conversations, dialogues and links) from the game <a href="https://discoelysium.com/">Disco Elysium</a>.</p>

<p><strong>Disco Elysium, its characters, dialogue and all other game content are the property of <a href="https://zaumstudio.com/">ZA/UM</a>.</strong> This repository only redistributes that data for personal, non-commercial research/tooling purposes and claims no ownership over it and is not affiliated with or endorsed by ZA/UM.</p>

<h1>Usage</h1>

<p><code>/postgres</code> contains a <code>Dockerfile</code> and a PostgreSQL dump (<code>dump.sql</code>) migrated from the game's exported dialogue tables via <a href="https://github.com/dimitri/pgloader">pgloader</a>.</p>

<pre><code>docker build -t disco-postgres ./postgres
docker run -d --name disco-postgres -p 5432:5432 disco-postgres</code></pre>

<p>This starts a Postgres server on <code>localhost:5432</code> with database <code>disco</code>, user <code>disco</code>, password <code>disco</code> (override via <code>-e POSTGRES_DB=... -e POSTGRES_USER=... -e POSTGRES_PASSWORD=...</code>), with the tables living in the <code>disco</code> schema: <code>disco.actors</code>, <code>disco.conversations</code>, <code>disco.dialogues</code> and <code>disco.links</code>.</p>
