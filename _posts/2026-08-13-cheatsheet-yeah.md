---
title: Cheatsheet Yea!
date: 2026-08-13 9:25:30 +0700
categories: [infrastructure, security]
tags: [cloud, infrastructure, security, portofolio]     # TAG names should always be lowercase
---

![TEXT](/assets/img/post/mencontek.jpg)

A collection of practical cheatsheets, commands, configurations, and quick references for my day-to-day work and technical experiments. These notes are intended as a quick reference for System Administration, Cloud, Cloud Security, Networking, and Infrastructure.

## Disclaimer

> These cheatsheets are based on my own notes, experience, and lab environments. Always verify commands and configurations before using them in production..
{: .prompt-tip }


## DBA

### Config Database PostgreSQL
```bash
SHOW work_mem; --8MB [appropriate per-query value] 
SHOW shared_buffers; --22138345 [25% of RAM]
SHOW maintenance_work_mem; --4175MB [for VACUUM/CREATE INDEX]
SHOW random_page_cost;
SHOW jit; 
SHOW wal_compression;
SHOW max_parallel_workers_per_gather;--2
SHOW max_parallel_maintenance_workers;
SHOW max_worker_processes;
SHOW max_parallel_workers;
SHOW parallel_setup_cost;
SHOW parallel_tuple_cost;
SHOW effective_io_concurrency; --number of SSD/disk spindles
SHOW autovacuum_vacuum_cost_delay;
SHOW autovacuum_vacuum_cost_limit;

SELECT pg_reload_conf(); --reload config not restart service

SET work_mem = '128MB';
ALTER SYSTEM SET shared_buffers = '64GB';
ALTER SYSTEM SET maintenance_work_mem = '4GB';
ALTER SYSTEM SET max_parallel_workers_per_gather = 4;
SELECT pg_reload_conf();

select *
from pg_settings 
where name like '%autovacuum%'
```
### Relate for Index Database PostgreSQL
```sql
--Check Existing Index on a table
SELECT tablename, indexname, indexdef
FROM pg_indexes  
WHERE schemaname = 'public'  
AND tablename = 'stock_move' --sample table
```
```sql
--Check total of INDEX SCAN's of an index
SELECT
    relname AS table_name,
    indexrelname AS index_name,
    idx_scan AS index_scans,
    idx_tup_read AS tuples_read,
    idx_tup_fetch AS tuples_fetched
FROM
    pg_stat_user_indexes
WHERE schemaname = 'public'
AND indexrelname not in (select indexrelname from pg_stat_user_indexes where indexrelname ilike '%%uniq%%' or indexrelname ilike '%%pkey')
AND relname not in (select relname from pg_stat_user_indexes where relname ilike '%%rel' or relname ilike '%%tax')
AND relname = 'stock_picking'
--AND relname in (select relname from pg_stat_user_indexes where relname ilike 'dms%%')
ORDER BY idx_scan ASC;
```

```sql
--FETCH RATIO dari index
SELECT
  indexrelname,
  idx_scan,
  idx_tup_read,
  idx_tup_fetch,
  ROUND((idx_tup_fetch::float/GREATEST(idx_tup_read,1))::numeric,2) AS fetch_ratio
FROM pg_stat_user_indexes
WHERE relname = 'stock_quant';
```

```sql
--INDEX SIZE
SELECT i.relname "Table Name",indexrelname "Index Name",
 pg_size_pretty(pg_relation_size(relid)) as "Table Size",
 pg_size_pretty(pg_relation_size(indexrelid)) "Index Size",
 pg_size_pretty(pg_total_relation_size(relid)) As "Total Size",
 pg_size_pretty(pg_indexes_size(relid)) as "Total Size of all Indexes",
 reltuples::bigint "Estimated table row count"
 FROM pg_stat_all_indexes i JOIN pg_class c ON i.relid=c.oid 
 WHERE i.relname='stock_quant'
 ```

```sql
--INDEX SIZE (TOTAL of a table)
SELECT relname, relpages, reltuples::numeric, pg_size_pretty(pg_table_size(oid)) "Table Size", pg_size_pretty(pg_indexes_size(oid)) as "Total Size of all Indexes"
FROM pg_class WHERE oid='stock_move'::regclass;
```

```sql
--Cek MISSING INDEX (tabel seq scan VS index scan)
SELECT 
('"' || relname || '"')::regclass
, seq_scan-idx_scan AS too_much_seq
, case when seq_scan-idx_scan>0 THEN 'Missing Index?' ELSE 'OK' END
,  pg_relation_size(relid::regclass) AS rel_size  
, seq_scan, idx_scan
FROM pg_stat_all_tables 
WHERE schemaname='public' 
AND pg_relation_size(relid::regclass)>80000 
ORDER BY too_much_seq DESC;
```

```sql
--CREATE DROP INDEX
CREATE INDEX [IF NOT EXISTS] dms_api_log_model_name_transaction_id_idx
ON dms_api_log(model_name, transaction_id);
DROP INDEX dms_api_log_response_idx;
```
### Relate for Table Database PostgreSQL
```sql
--TABLE SIZE
SELECT pg_size_pretty(pg_total_relation_size('mail_message'));
 ```

```sql
--AUTO VACUUM/ ANALYZE and Dead Tuple
SELECT relname, n_live_tup, n_dead_tup, trunc(100*n_dead_tup/(n_live_tup+1))::float "ratio%",
to_char(last_autovacuum, 'YYYY-MM-DD HH24:MI:SS') as autovacuum_date,
to_char(last_autoanalyze, 'YYYY-MM-DD HH24:MI:SS') as autoanalyze_date
FROM pg_stat_all_tables
ORDER BY 2 desc;
```

```sql
--Check toast table
select relname from pg_class where oid = (select reltoastrelid from pg_class where relname='dms_api_log')
select reltoastrelid,* from pg_class where relname = 'pg_toast_581857'
select * from pg_toast.pg_toast_581857
```

```sql
--**Check query with biggest IOPS ussage (1 block = 8kb)
SELECT (blk_read_time + blk_write_time)::int,shared_blks_dirtied + shared_blks_written as block, query,*
FROM pg_stat_statements
ORDER BY 2 DESC
LIMIT 30;
```

```sql
--Check last analyze
SELECT relname, n_live_tup, n_dead_tup, last_analyze::date, last_autoanalyze::date, last_vacuum::date, last_autovacuum::date
FROM pg_stat_all_tables
where n_live_tup >0
order by n_dead_tup desc;
```
```sql
--terminate query locking
SELECT pg_terminate_backend((SELECT 
  blockinga.pid AS blocking_pid
FROM pg_catalog.pg_locks blockedl
JOIN pg_stat_activity blockeda ON blockedl.pid = blockeda.pid
JOIN pg_catalog.pg_locks blockingl ON(
  ( (blockingl.transactionid=blockedl.transactionid) OR
  (blockingl.relation=blockedl.relation AND blockingl.locktype=blockedl.locktype)
  ) AND blockedl.pid != blockingl.pid)
JOIN pg_stat_activity blockinga ON blockingl.pid = blockinga.pid
  AND blockinga.datid = blockeda.datid
WHERE NOT blockedl.granted
AND blockinga.datname = current_database()
limit 1));
```

### IAM Access Database PostgreSQL
```sql
--check database owner
SELECT datname, rolname
FROM pg_database d
JOIN pg_roles r ON (d.datdba = r.oid)
WHERE r.rolname = 'sampleuser';
```

```sql
--check table privileges for a user
SELECT table_catalog, table_schema, table_name, privilege_type
FROM information_schema.table_privileges
WHERE grantee = 'sampleuser';
```

```sql
--count privileges per schema
SELECT 
    table_schema,
    privilege_type,
    COUNT(*) AS jumlah_tabel
FROM information_schema.table_privileges
WHERE grantee = 'sampleuser'
GROUP BY table_schema, privilege_type
ORDER BY table_schema, privilege_type;
```

```sql
--audit privileges semua user kecuali beberapa role
SELECT 
    grantee,
    table_schema,
    privilege_type,
    COUNT(*) AS jumlah_tabel
FROM information_schema.table_privileges
WHERE grantee NOT IN ('rdsadmin','rds_superuser', 'odoo')
GROUP BY grantee, table_schema, privilege_type
ORDER BY grantee, table_schema, privilege_type;
```

```sql
--revoke write privileges
REVOKE INSERT, UPDATE, DELETE, TRUNCATE, REFERENCES, TRIGGER 
ON ALL TABLES IN SCHEMA public FROM sampleuser;
```

### User / Role Management Database PostgreSQL
```sql
--for security audit, terutama mencari user yang punya: SUPERUSER, CREATEROLE, CREATEDB, REPLICATION, BYPASSRLS
SELECT
    rolname,
    rolsuper,
    rolinherit,
    rolcreaterole,
    rolcreatedb,
    rolcanlogin,
    rolreplication,
    rolbypassrls
FROM pg_roles
ORDER BY rolname;
```
```sql
--check apakah user bisa login?
SELECT
    rolname,
    rolcanlogin
FROM pg_roles
WHERE rolname = 'sampleuser';
```


## Sysadmin

## Notes

This page will continue to evolve as I encounter new problems, learn new technologies, and build new infrastructure. The goal is to keep these references practical, concise, and useful during troubleshooting or implementation.

If a command or configuration is useful enough to remember, it probably belongs here.