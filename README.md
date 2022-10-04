# pgbackrest_install
Pgbackrest installation and local backups instruction

Install all soft:

``apt update && apt install install build-essential libssl-dev libxml2-dev libperl-dev zlib1g-dev libpq-dev perl pgbackrest``

Check installation:

``pgbackrest version``

Add backups directory:

``sudo mkdir /postgresbackups``

``sudo chown postgres:postgres /postgresbackups``

``sudo mkdir /postgresbackups/logs``

``sudo chown postgres:postgres /postgresbackups/logs``

Change postgres config:

``sudo nano /etc/postgresql/14/main/postgresql.conf``

Add this code:

```
archive_command = 'pgbackrest --stanza=main archive-push %p'
archive_mode = on
max_wal_senders = 3
wal_level = logical
```

Create pgbackrest config:

``sudo nano /etc/pgbackrest.conf``

All script code:

```
[global]
repo1-path=/pgbckps
process-max=2
log-path=/pgbckps/logs

[main]
pg1-path=/var/lib/postgresql/14/main
retention-diff=3
retention-full=2
start-fast=y
stop-auto=y
```

Reload postgres demon:

``sudo systemctl restart postgresql``

Check:

``sudo systemctl status postgresql``

Step 1. Create stanza:

``sudo -u postgres pgbackrest --stanza=testprod --log-level-console=info stanza-create``

Answer:

```
2021-07-19 15:08:35.517 P00   INFO: stanza-create command begin 2.34: --exec-id=10010-6babed2c --log-level-console=info --log-path=/mnt/pgbak/log --pg1-path=/var/lib/pgsql/13/data --repo1-path=/mnt/pgbak --stanza=testprod
2021-07-19 15:08:36.128 P00   INFO: stanza-create for stanza 'testprod' on repo1
2021-07-19 15:08:36.146 P00   INFO: stanza-create command end: completed successfully (629ms)
```

