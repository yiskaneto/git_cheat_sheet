# Postgres Cheat Sheet

## Table of contents

- [General](#general)
- [Host](#host)
- [Time](#time)
- [Databases](#databases)

## General

`\copyright` for distribution terms
`\h` for help with SQL commands
`\?` for help with psql commands
`\g` or terminate with semicolon to execute query
`\q` to quit

## Host

- `\conninfo`: Shows connection info like, user, database, ip and protocols

## Time

- Current time:
`select now();`

## Databases

- List of databases:

    ```bash
    \l
    ```

- Connect to another database

    ```bash
    \c[onnect]
    ```

- show all dbs:

    ```bash
        \l
    ```

- Showing all tables:

    ```bash
    \dt;
    ```

- Showing tables not  using pg_catalog schema (similart to \dt)

    ```bash
    SELECT * FROM pg_catalog.pg_tables WHERE schemaname != 'pg_catalog' AND schemaname != 'information_schema';
    ```

- Showing tables using pg_catalog schema

    ```bash
    SELECT * FROM pg_catalog.pg_tables;
    ```

- Delete database:

    ```bash
    DROP DATABASE <database name>;
    ```

### Db backup (db dump)

- Create db dump using plain-text SQL script file format:

    ```bash
    pg_dump -h $(echo $DB_HOST | base64 -d) -F p <db name> > testo.sql

    example:
    pg_dump -h $(echo $DB_HOST | base64 -d) -F p kfs > testo.sql
    ```

- Create db dump using sql tar format:

    ```bash
    pg_dump -h $(echo $DB_HOST | base64 -d) -F t <db name> > <file name>.tar

    example:
    pg_dump -h $(echo $DB_HOST | base64 -d) -F t kfs > testo.tar
    ```

- Backup command run on init pod:

    ```bash
    KFS_BACKUP_FILE=$(echo "${DB_ENVIRONMENT}_kfs_backup_$(date +"%Y-%m-%d_%H-%M-%S").tar")
    pg_dump -h ${DB_HOSTNAME} --dbname=$DB_NAME -F t > ${KFS_BACKUP_FILE} && aws s3 cp ${KFS_BACKUP_FILE}s3://${S3_KFS_DB_BACKUP_BUCKET}/kfs-backups/${DB_ENVIRONMENT}/${KFS_BACKUP_FILE}
    ```

## Tables

`\d[S+]` : list tables, views, and sequences
`\d[S+]  NAME` : Describe table, view, sequence, or index
`\dg[S+] [PATTERN]`      list roles
`\di[S+] [PATTERN]`      list indexes
`\dn[S+] [PATTERN]`     list schemas

## Copy

- Copy

    ```bash
    \copy (SELECT * FROM public."fn_AccountMigrationReport"()) TO '/tmp/migration_user_report.csv' DELIMITER ',' CSV HEADER;
    ```

## User

- Print current user:

    ```bash
    SELECT current_user;
    ```
