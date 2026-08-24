<h1>Disco Elysium Explorer</h1>

<p>The project is a single-page apllication which allow you to build, read and listen conversation from the game - <a href="https://discoelysium.com/"> Disco Elysium </a> developed by studio <a href="https://zaumstudio.com/">ZA/UM</a>.</p>

<h1>Installation</h1>

<p>To install project on local you will need:</p>
<ul>
    <li>
        Apache or nginx to run backend (made on framework Yii2);
    </li>
    <li>
        Composer to install all dependencies;
    </li>
    <li>
        SQL database. To connect the database to project create file <code>/config/db.php</code> and write here connect parameters. There is an example of file: <code>/config/dbExample.php</code>.;
    </li>
    <li>
        Audio assets;
    </li>
</ul>

<p>All necesary SQL files are in folder <code>/migrations</code>. Each SQL file creates table and fills it with data.</p>

<p>Alternatively, <code>/postgres</code> contains a <code>Dockerfile</code> and a PostgreSQL dump (<code>dump.sql</code>, migrated from the <code>/migrations</code> MySQL dumps via <a href="https://github.com/dimitri/pgloader">pgloader</a>) that build into a ready-to-use database preloaded with all actors, conversations, dialogues and links:</p>

<pre><code>docker build -t disco-postgres ./postgres
docker run -d --name disco-postgres -p 5432:5432 disco-postgres</code></pre>

<p>This starts a Postgres server on <code>localhost:5432</code> with database <code>disco</code>, user <code>disco</code>, password <code>disco</code> (override via <code>-e POSTGRES_DB=... -e POSTGRES_USER=... -e POSTGRES_PASSWORD=...</code>), with the tables living in the <code>disco</code> schema (e.g. <code>disco.dialogues</code>). Since this project's models are written for MySQL, using this database with the app itself would require adjusting <code>/config/db.php</code> to a Postgres DSN and reviewing the Yii2 query code for MySQL-specific syntax.</p>

<p>Audio assets are not in this repository because there is too many of them (~50k files). But you can download them from <a href="https://drive.google.com/file/d/1hxRZf4zyn23hcer4_po4X0WyQIlewi5b/view?usp=sharing">Google Drive</a> or just <a href="https://www.reddit.com/user/Leerion">message me</a>.<p>
    
<p> After you got assets extract them in to foldee <code>/web/assets/AudioClip_aac/</code>.</p>

<h1>Demo</h1>
You can also test project <a href="http://134.0.119.41/">online</a>.
