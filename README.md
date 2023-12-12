* Run docker-compose up
* Run commands from `command.md`

Then we get the following error:

```
psql:schema.psql:81: ERROR:  permission denied for schema public
```

on the command `ALTER FUNCTION "public"."get_current_user"() OWNER TO "application_user";`

Question is how to handle schema dump (without manually changing dump) when we don't have access to superuser e.g. in cloud environments

If I add `GRANT CREATE ON SCHEMA public TO application_user` in source database, then it appears at the end of the dump and after ALTER FUNCTION.
