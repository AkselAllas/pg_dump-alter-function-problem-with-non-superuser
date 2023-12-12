```
PGPASSWORD=password psql -p 7432 -h 127.0.0.1 -U replicator -d test

CREATE ROLE application_user WITH LOGIN PASSWORD 'password';
GRANT application_user to current_user;

SET ROLE application_user;

CREATE OR REPLACE FUNCTION get_current_user()
RETURNS VARCHAR AS
$$
DECLARE
    current_user_name VARCHAR;
BEGIN
    SELECT current_user INTO current_user_name;
    RETURN current_user_name;
END;
$$
LANGUAGE plpgsql;

```

```
PGPASSWORD=password pg_dump --port 7432 -h 127.0.0.1 -U "replicator" --schema-only --verbose \
       --quote-all-identifiers \
       --exclude-schema=pg_catalog \
       --create --clean --if-exists test > schema.psql
```

```
PGPASSWORD=password psql -p 9432 -h 127.0.0.1 -U replicator -d postgres
DROP DATABASE test;

CREATE ROLE application_user WITH LOGIN PASSWORD 'password';
CREATE ROLE schema_dump_reader WITH Createrole CreateDB Replication LOGIN PASSWORD 'password';
GRANT application_user to current_user;
GRANT application_user to schema_dump_reader;
REVOKE application_user FROM current_user;
```

```
PGPASSWORD=password psql -h 127.0.0.1 --port 9432 -U "schema_dump_reader" --echo-all -f schema.psql postgres
```
