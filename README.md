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

- Create a test table for checking is it working or not.
```sql
CREATE TABLE postgresql_cron_job_test (
    id SERIAL PRIMARY KEY,
    job_name TEXT NOT NULL,
    schedule TEXT NOT NULL,
    last_run TIMESTAMP DEFAULT now()
);
```

- Syntax of enable a cronjob
```sql
SELECT cron.schedule('< cron expression >', $$ < SQL Command | Calling Procedure | Calling Function | Calling Trigger > $$ );
```

- Example of a cronjob
```sql
SELECT cron.schedule('*/1 * * * *', $$INSERT INTO postgresql_cron_job_test (job_name, schedule) 
VALUES 
    ('Daily Cleanup', '0 2 * * *'),
    ('Weekly Backup', '0 3 * * 0'),
    ('Hourly Report', '0 * * * *'),
    ('Database Vacuum', '30 1 * * *');$$);
```
This cronjob run every minute and insert the defined 5 rows in the `postgresql_cron_job_test` table.

- See the all schedule jobs from cronjob
```sql
select * from cron.job;
```

- To unschedule or remove a job from scheduling find the `jobid` from the  `select * from cron.job;` table and run `SELECT cron.unschedule(jobid);`
```sql
SELECT cron.unschedule(jobid);
```

- cron job details including starting time,error message and end time.
```sql
select * from cron.job_run_details order by start_time desc limit 5;
```


- Use background worker instead of connection in `postgresql.conf` file.
```bash
# Schedule jobs via background workers instead of localhost connections
cron.use_background_workers = on
# Increase the number of available background workers from the default of 8
max_worker_processes = 20
```

- Change the default `cron.max_running_jobs	32` as your need in `postgresql.conf` file.
```bash
cron.max_running_jobs = 50
```

Maximum number of jobs that can be running at the same time.


