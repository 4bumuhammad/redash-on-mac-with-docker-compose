# &#x1F6A9; Redash on Mac with docker-compose

Running Redash locally on MacOS.

Prerequisites: `docker-compose`

### &#x1FAB4; Steps :

- `0.` -  clone the repo and cd into the dir
- `1.` -  `make setup` - one time setup (first time only)
- `2.` -  `make start` - starts the service on http://localhost:8000/
- `3.` -  `make stop` - stops the service

### overview during the development process :

step - 1
<p align="center">
    <img src="./gambar-petunjuk/ss_step_1.png" alt="ss_step" style="display: block; margin: 0 auto;">
</p>

    ❯ docker images

        REPOSITORY      TAG            IMAGE ID       CREATED         SIZE
        redis           5.0-alpine     8ab49366732f   16 months ago   22.6MB
        postgres        9.6-alpine     a368e0e3b16f   2 years ago     53.1MB
        redash/redash   8.0.0.b32245   1b48a51810b5   4 years ago     1.31GB


    ❯ docker ps -a

        CONTAINER ID   IMAGE                 COMMAND                  CREATED         STATUS         PORTS      NAMES
        a5581f81ddff   postgres:9.6-alpine   "docker-entrypoint.s…"   8 minutes ago   Up 8 minutes   5432/tcp   redash-on-mac-with-docker-compose-postgres-1
        d52b9f5cec04   redis:5.0-alpine      "docker-entrypoint.s…"   8 minutes ago   Up 8 minutes   6379/tcp   redash-on-mac-with-docker-compose-redis-1


into the postgres container

    ❯ docker exec -it redash-on-mac-with-docker-compose-postgres-1 /bin/sh

        / # psql --version
        psql (PostgreSQL) 9.6.24

        / # psql -h localhost -p 5432 -U postgres
        psql (9.6.24)
        Type "help" for help.

        postgres=# \l
                                         List of databases
           Name    |  Owner   | Encoding |  Collate   |   Ctype    |   Access privileges   
        -----------+----------+----------+------------+------------+-----------------------
         postgres  | postgres | UTF8     | en_US.utf8 | en_US.utf8 | 
         template0 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
                   |          |          |            |            | postgres=CTc/postgres
         template1 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
                   |          |          |            |            | postgres=CTc/postgres
        (3 rows)


        postgres=# \c postgres 
        You are now connected to database "postgres" as user "postgres".

        postgres=# \d
                                List of relations
        Schema |               Name               |   Type   |  Owner   
        --------+----------------------------------+----------+----------
        public | access_permissions               | table    | postgres
        public | access_permissions_id_seq        | sequence | postgres
        public | alembic_version                  | table    | postgres
        public | alert_subscriptions              | table    | postgres
        public | alert_subscriptions_id_seq       | sequence | postgres
        public | alerts                           | table    | postgres
        public | alerts_id_seq                    | sequence | postgres
        public | api_keys                         | table    | postgres
        public | api_keys_id_seq                  | sequence | postgres
        public | changes                          | table    | postgres
        public | changes_id_seq                   | sequence | postgres
        public | dashboards                       | table    | postgres
        public | dashboards_id_seq                | sequence | postgres
        public | data_source_groups               | table    | postgres
        public | data_source_groups_id_seq        | sequence | postgres
        public | data_sources                     | table    | postgres
        public | data_sources_id_seq              | sequence | postgres
        public | events                           | table    | postgres
        public | events_id_seq                    | sequence | postgres
        public | favorites                        | table    | postgres
        public | favorites_id_seq                 | sequence | postgres
        public | groups                           | table    | postgres
        public | groups_id_seq                    | sequence | postgres
        public | notification_destinations        | table    | postgres
        public | notification_destinations_id_seq | sequence | postgres
        public | organizations                    | table    | postgres
        public | organizations_id_seq             | sequence | postgres
        public | queries                          | table    | postgres
        public | queries_id_seq                   | sequence | postgres
        public | query_results                    | table    | postgres
        public | query_results_id_seq             | sequence | postgres
        public | query_snippets                   | table    | postgres
        public | query_snippets_id_seq            | sequence | postgres
        public | users                            | table    | postgres
        public | users_id_seq                     | sequence | postgres
        public | visualizations                   | table    | postgres
        public | visualizations_id_seq            | sequence | postgres
        public | widgets                          | table    | postgres
        public | widgets_id_seq                   | sequence | postgres
        (39 rows)


        postgres=# \q
        / # exit

step - 2
<p align="center">
    <img src="./gambar-petunjuk/ss_step_2.png" alt="ss_step" style="display: block; margin: 0 auto;">
</p>

    ❯ docker images

        REPOSITORY      TAG            IMAGE ID       CREATED         SIZE
        redis           5.0-alpine     8ab49366732f   16 months ago   22.6MB
        postgres        9.6-alpine     a368e0e3b16f   2 years ago     53.1MB
        redash/nginx    latest         2a6299d40898   3 years ago     134MB
        redash/redash   8.0.0.b32245   1b48a51810b5   4 years ago     1.31GB



    ❯ docker ps -a --format "table {{.ID}}\t{{.Image}}\t{{.Status}}\t{{.Names}}\t{{.Ports}}"

        CONTAINER ID   IMAGE                        STATUS         NAMES                                                  PORTS
        50194c1b3264   redash/nginx:latest          Up 4 minutes   redash-on-mac-with-docker-compose-nginx-1              443/tcp, 0.0.0.0:8000->80/tcp
        560a336cf1ae   redash/redash:8.0.0.b32245   Up 4 minutes   redash-on-mac-with-docker-compose-scheduled_worker-1   5000/tcp
        63bcaa664b8c   redash/redash:8.0.0.b32245   Up 4 minutes   redash-on-mac-with-docker-compose-server-1             0.0.0.0:5000->5000/tcp
        47ab15c89890   redash/redash:8.0.0.b32245   Up 4 minutes   redash-on-mac-with-docker-compose-adhoc_worker-1       5000/tcp
        4a89f691b1c5   redash/redash:8.0.0.b32245   Up 4 minutes   redash-on-mac-with-docker-compose-scheduler-1          5000/tcp
        a34f6c5c39b5   redis:5.0-alpine             Up 4 minutes   redash-on-mac-with-docker-compose-redis-1              6379/tcp
        c903fb2a4eeb   postgres:9.6-alpine          Up 4 minutes   redash-on-mac-with-docker-compose-postgres-1           5432/tcp


file structure :

    ❯ tree -L 2 -I 'gambar-petunjuk|README.md'

            ├── Makefile
            ├── docker-compose.yml
            ├── env
            └── postgres-data
                ├── PG_VERSION
                ├── base
                ├── global
                ├── pg_clog
                ├── pg_commit_ts
                ├── pg_dynshmem
                ├── pg_hba.conf
                ├── pg_ident.conf
                ├── pg_logical
                ├── pg_multixact
                ├── pg_notify
                ├── pg_replslot
                ├── pg_serial
                ├── pg_snapshots
                ├── pg_stat
                ├── pg_stat_tmp
                ├── pg_subtrans
                ├── pg_tblspc
                ├── pg_twophase
                ├── pg_xlog
                ├── postgresql.auto.conf
                ├── postgresql.conf
                ├── postmaster.opts
                └── postmaster.pid

---

### &#x1F525; Result :

<p align="center">
    <img src="./gambar-petunjuk/ss_redash_001.png" alt="ss_redash_001" style="display: block; margin: 0 auto;">
</p>

<p align="center">
    <img src="./gambar-petunjuk/ss_redash_002-1.png" alt="ss_redash_002-1" style="display: block; margin: 0 auto;">
</p>

<p align="center">
    <img src="./gambar-petunjuk/ss_redash_003.png" alt="ss_redash_003" style="display: block; margin: 0 auto;">
</p>

<p align="center">
    <img src="./gambar-petunjuk/ss_redash_004.png" alt="ss_redash_004" style="display: block; margin: 0 auto;">
</p>

<p align="center">
    <img src="./gambar-petunjuk/ss_redash_005.png" alt="ss_redash_005" style="display: block; margin: 0 auto;">
</p>




step - 3

    ❯ make stop

        docker-compose stop
        [+] Running 7/7
        ⠿ Container redash-on-mac-with-docker-compose-adhoc_worker-1      Stopped                                3s
        ⠿ Container redash-on-mac-with-docker-compose-scheduler-1         Stopped                                3s
        ⠿ Container redash-on-mac-with-docker-compose-scheduled_worker-1  Stopped                                3s
        ⠿ Container redash-on-mac-with-docker-compose-nginx-1             Stopped                                2s
        ⠿ Container redash-on-mac-with-docker-compose-server-1            Stopped                                9s
        ⠿ Container redash-on-mac-with-docker-compose-postgres-1          Stopped                                2s
        ⠿ Container redash-on-mac-with-docker-compose-redis-1             Stopped


    ❯ docker ps -a --format "table {{.ID}}\t{{.Image}}\t{{.Status}}\t{{.Names}}\t{{.Ports}}"

        CONTAINER ID   IMAGE                        STATUS                     NAMES                                                  PORTS
        50194c1b3264   redash/nginx:latest          Exited (0) 2 minutes ago   redash-on-mac-with-docker-compose-nginx-1              
        560a336cf1ae   redash/redash:8.0.0.b32245   Exited (0) 2 minutes ago   redash-on-mac-with-docker-compose-scheduled_worker-1   
        63bcaa664b8c   redash/redash:8.0.0.b32245   Exited (0) 2 minutes ago   redash-on-mac-with-docker-compose-server-1             
        47ab15c89890   redash/redash:8.0.0.b32245   Exited (0) 2 minutes ago   redash-on-mac-with-docker-compose-adhoc_worker-1       
        4a89f691b1c5   redash/redash:8.0.0.b32245   Exited (0) 2 minutes ago   redash-on-mac-with-docker-compose-scheduler-1          
        a34f6c5c39b5   redis:5.0-alpine             Exited (0) 2 minutes ago   redash-on-mac-with-docker-compose-redis-1              
        c903fb2a4eeb   postgres:9.6-alpine          Exited (0) 2 minutes ago   redash-on-mac-with-docker-compose-postgres-1  

    ❯ docker rm -f $(docker ps -aq) && docker rmi -f $(docker images -q)

        50194c1b3264
        560a336cf1ae
        63bcaa664b8c
        47ab15c89890
        4a89f691b1c5
        a34f6c5c39b5
        c903fb2a4eeb
        Untagged: redis:5.0-alpine
        Untagged: redis@sha256:1a3c609295332f1ce603948142a132656c92a08149d7096e203058533c415b8c
        Deleted: sha256:8ab49366732f99db61af22921fef4e2e63c516d17578c805f9ae052225229d8e
        Deleted: sha256:18c90e145e052a0e63287e8718ef26064156935975550bd9fa946d818ca7b04e
        Deleted: sha256:56199550bf767b8f8fb5e38917a3dac6b9695fbd00ed37adabe362d62474e6ec
        Deleted: sha256:6e62a5fc0b62573972d7ba295eeacf47390e5c0187a6db4045d24c34df18e97c
        Deleted: sha256:bdb4cb5178133d1212199a06e6a1382a25bbc3bdd123357c377c856144f3b376
        Deleted: sha256:7043d20aedcf193fb1fbe343e3003fb7520b15d9c66b19c15cf2aa98d8db2856
        Deleted: sha256:17bec77d7fdc6988cd96b3051b4ad4d3cd6031b2faf0581468be64aac0acc20b
        Untagged: postgres:9.6-alpine
        Untagged: postgres@sha256:8342bcb43446694428ec6594e72e4299692854f0fc3aca090b0ab46f4c7f32a1
        Deleted: sha256:a368e0e3b16fec068853171da17fb3a1b882a1bf09a57234c7ecb12822b44462
        Deleted: sha256:2f7bff351a9764cb26d320cc73679a34d7f876e489092271e0725f2fb08b2c62
        Deleted: sha256:31b14dc87757174b882f89873c45e2924aa062e5897f26bf3a76e4d6ec32aae1
        Deleted: sha256:7eabbd2139ad69dadc69c4eb287edea572f79569441c94d9d1543811d914c6fd
        Deleted: sha256:51b00b63dd1d8410ac0de6bc8ab0114bee2bf9ca5133fb4122b804b298322e45
        Deleted: sha256:d0ffb6530350de623aac08a99705a360832dc1e4f3e429a1b2711b390e3d50db
        Deleted: sha256:6399a2787ce157e54747d24d131c4b3c685a895b285bd1517eba9ca8b9ea10c2
        Deleted: sha256:19d7930912ae9c15564f0f996976e2aaac8726f8ccfe133f82f00f91c32f27e5
        Deleted: sha256:5eda8717f164c3b50fe7dc5de058e3954a95826de899a992775e3cf6ec39e9ad
        Deleted: sha256:07d3c46c9599a88fa81c385391300ab877b98ef71b18cce942455025657edeb5
        Untagged: redash/nginx:latest
        Untagged: redash/nginx@sha256:4eaaa7af6476b0422058b0022661ad6129dfbf9065c506fb0904bbf0a16f2007
        Deleted: sha256:2a6299d408989383e078d491f816cedd19119124131428c21fdeaf2abde2370c
        Deleted: sha256:c2f41f0f226d66bfe995a2187767b5d264ac634010b89af7a7dca8cc753b7977
        Deleted: sha256:b3ff591eba7ef5c0b0f5cb094dc09c236ae5317c7a44823da4b6e179cba2bf76
        Deleted: sha256:04b58d413675d437ddeee595ae1c0007ae5fef4e937b301bfe7067d7a199e3f6
        Deleted: sha256:0f0e193b5be50cec6e090a4c654c9295e827ec30ecc3c3a11686907212bf54a4
        Deleted: sha256:4c284ce71b0301e0e898c61bbb22c625bcb26c1ee6d57724ded67015542656c7
        Deleted: sha256:9084993125a652ea5921208ee05c913c38d0f9ed52e55ef3cea15116039d9d73
        Deleted: sha256:bac8bbe93f6c6a9f753916e530e2f1bd89cddf8776c1864caab30698e1a7abdb
        Deleted: sha256:0c650b3801622851e7b86058f914be69af2d1425ab9f834e26b29975ed193df9
        Deleted: sha256:78dbfa5b7cbc2bd94ccbdba52e71be39b359ed7eac43972891b136334f5ce181
        Untagged: redash/redash:8.0.0.b32245
        Untagged: redash/redash@sha256:55cedc91a09107e894a0ce872d138ec8c708ec537f6df282207bdf14eec72491
        Deleted: sha256:1b48a51810b526fe2edf645e0bbcf8d43bdccf13130e8ec9996ff671c48f8aa5
        Deleted: sha256:da820f0d4c847a3120614fc2a107ffa12d3606da8070c3ee0cc023f0657bb6dd
        Deleted: sha256:833127766220edc81a22ab40e80c8509981a16cd7ab21ea642a2676762b408ae
        Deleted: sha256:70364ef16788eb81043967abe2969b4952ab7874187fb1c17cc2c258346aadb8
        Deleted: sha256:7a7a80b748727733f3b542e000b49c01785cba7ed667c2fbbd4382e37efaf752
        Deleted: sha256:a12df288fb174c2153513ede33d85b35ebc0694c3b55f4e9b500462466ebb4c1
        Deleted: sha256:15ff01340f24458a012f22d7069fbd56f541e9eca6c5d96683e595b07ba158b8
        Deleted: sha256:7e2cb14223c1794eda46309bec7684b12457e7d434111fe6802bc55bae21b707
        Deleted: sha256:1761a2cbef531aa932bb7536ab648aaa8e6028612ce5be76925310ba4f65301a
        Deleted: sha256:5b1633702048bbee0c927e4d98bb2d67b1b24c10709a5e241dc7d5b24db7fe11
        Deleted: sha256:736247e9c3164bceefc53d72d55fc21f4d4950bb9d7035e103f68606f3014b56
        Deleted: sha256:4737084096322d47a284940cb5d3aabf7d426cbc1b96bb1328dec3783ed1bc18
        Deleted: sha256:034def79cbb3dba4dbe4c09705cdcf885de9c0f24fbad3919b0072c4e236fc97
        Deleted: sha256:1c95c77433e8d7bf0f519c9d8c9ca967e2603f0defbf379130d9a841cca2e28e

---

### &#x1FAA7; Notes : 

    ❯ docker --version

        Docker version 20.10.14, build a224086