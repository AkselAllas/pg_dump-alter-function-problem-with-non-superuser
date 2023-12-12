UPDATE: Seems to be working as intended. And pg_dump isn't guaranteed to work in managed postgresql.
https://www.postgresql.org/message-id/9ad3c3781f8d89efab1917d1431226f4da79d443.camel%40cybertec.at


When making a dump from PostgreSQL 14 and using it in PostgreSQL 15 with a user that has no superuser access, then we run into the following line from https://www.postgresql.org/docs/15/sql-alterfunction.html

> To alter the owner, you must also be a direct or indirect member of the new owning role, and that role must have CREATE privilege on the function's schema.

To reproduce:
* Run docker-compose up
* Run commands from `command.md`

Then we get the following error:

```
psql:schema.psql:81: ERROR:  permission denied for schema public
```

on the command `ALTER FUNCTION "public"."get_current_user"() OWNER TO "application_user";`

Question is how to handle schema dump (without manually changing dump) when we don't have access to superuser e.g. in cloud environments

If I add `GRANT CREATE ON SCHEMA public TO application_user` in source database, then it appears at the end of the dump and after ALTER FUNCTION.
