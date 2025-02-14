# ⏳ Scheduling in postgreSQL using `pg_cron` 
`pg_cron` is a PostgreSQL extension that allows scheduling of SQL-based cron jobs directly inside the database. It is useful for automating tasks like vacuuming tables, updating statistics, or performing regular data maintenance.

- Download the `pg_cron` extention

```bash
sudo yum install -y pg_cron_16
```

- 🔧 Set some configuration in `postgresql.conf` file. `shared_preload_libraries` alreadly in there we need to add `cron.timezone` and `cron.database_name` manually at the bottom of the file. 

```bash
shared_preload_libraries = 'pg_cron' 
cron.timezone = 'Asia/Dhaka' 
cron.database_name = 'postgres' 
```

- Restart the postgreSQL service

```bash
sudo systemctl restart postgresql-16
```

- Go to postgres database and create the pg_cron extension for this database.
```bash
sudo -i -u postgres psql
```
```sql
CREATE EXTENSION pg_cron;
```




