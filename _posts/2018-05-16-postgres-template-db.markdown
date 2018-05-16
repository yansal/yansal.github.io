---
layout: post
title:  "Postgres template database"
date:   2018-05-16 00:00:00
---

I'm working on an application with a large database schema. When running unit tests, it needs to connect to a fresh database with the right schema and zero data inside.

There are a couple of solutions. I was inspired from the answers of this stack overflow question: <https://stackoverflow.com/q/2829158>

## Drop the database, create a new one and apply the schema

This is the simplest solution, however it becomes slower and slower as the schema becomes larger. If it takes more than a second, it's too slow.

## Truncate all tables

Something like running this function.

```sql
DO $$BEGIN EXECUTE
(
    SELECT 'TRUNCATE TABLE ' || string_agg(oid::regclass::text, ', ')
    FROM pg_class
    WHERE relkind = 'r' -- only tables
    AND relnamespace = 'public'::regnamespace
);
END$$
```

It's actually quite fast. It feels a bit hackish to build a sql string and then execute it, and at the same time it's interesting to see how Postgres stores internal things.

This solution also has two small downsides that may matter. First it truncates all tables including bootstrap data that may be required. Second it doesn't reset sequences.

## Drop the database and create a new one from a template

That's the solution I prefer, because it's fast enough and simpler than the previous one.

First create a template database and apply the schema.

```bash
$ createdb my_test_tmpl && psql -c "alter database my_test_tmpl is_template true"
```

Then before running unit tests, drop the test database and create it from the template.

```bash
$ dropdb my_test && createdb my_test -T my_test_tmpl
```

When the schema moves, update the template database.
