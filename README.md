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

``sudo -u postgres pgbackrest --stanza=main --log-level-console=info stanza-create``

Answer:

```
2021-07-19 15:08:35.517 P00   INFO: stanza-create command begin 2.34: --exec-id=10010-6babed2c --log-level-console=info --log-path=/mnt/pgbak/log --pg1-path=/var/lib/pgsql/13/data --repo1-path=/mnt/pgbak --stanza=testprod
2021-07-19 15:08:36.128 P00   INFO: stanza-create for stanza 'testprod' on repo1
2021-07-19 15:08:36.146 P00   INFO: stanza-create command end: completed successfully (629ms)
```

Step 2. Check stanza:

``sudo -u postgres pgbackrest --stanza=main --log-level-console=info check``

Answer:

```
2022-10-04 12:31:18.239 P00   INFO: check command begin 2.41: --exec-id=2907186-ee921fba --log-level-console=info --log-path=/pgbckps/logs --pg1-path=/var/lib/postgresql/14/main --repo1-path=/pgbckps --stanza=main
2022-10-04 12:31:18.849 P00   INFO: check repo1 configuration (primary)
2022-10-04 12:31:19.052 P00   INFO: check repo1 archive for WAL (primary)
2022-10-04 12:31:19.255 P00   INFO: WAL segment 00000001000000000000000E successfully archived to '/pgbckps/archive/main/14-1/0000000100000000/00000001000000000000000E-8bfa3ff412942ffddb0a158bf2fea08b2a01befc.gz' on repo1
2022-10-04 12:31:19.255 P00   INFO: check command end: completed successfully (1017ms)
```

In the /postgresbackups folder create subfolders for backups:

```
sudo tree -d /postgresbackups
/postgresbackups
├── archive
│   └── main
│       └── 14-1
│           └── 0000000100000000
├── backup
│   └── main
└── logs
```

