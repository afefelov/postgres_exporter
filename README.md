# PostgresSQL Server Exporter

Prometheus exporter for PostgresSQL server metrics.
Supported Postgres versions: 9.1 and up.

## Building and running

    make
    export DATA_SOURCE_NAME="postgres://postgres:password@localhost/?sslmode=disable"
    ./postgres_exporter <flags>

See the [github.com/lib/pq](http://github.com/lib/pq) module for other ways to format the connection string.

### Flags

Name               | Description
-------------------|------------
web.listen-address | Address to listen on for web interface and telemetry.
web.telemetry-path | Path under which to expose metrics.
auth.user          | User for basic http authentification.
auth.pass          | Password for basic http authentification.

### Setting the Postgres server's data source name

The PostgresSQL server's [data source name](http://en.wikipedia.org/wiki/Data_source_name)
must be set via the `DATA_SOURCE_NAME` environment variable.

For running it locally on a default Debian/Ubuntu install, this will work (transpose to init script as appropriate):

    sudo -u postgres DATA_SOURCE_NAME="user=postgres host=/var/run/postgresql/ sslmode=disable" postgres_exporter

### Adding new metrics

The exporter will attempt to dynamically export additional metrics if they are added in the
future, but they will be marked as "untyped". Additional metric maps can be easily created
from Postgres documentation by copying the tables and using the following Python snippet:

```python
x = """tab separated raw text of a documentation table"""
for l in StringIO(x):
    column, ctype, description = l.split('\t')
    print """"{0}" : {{ prometheus.CounterValue, prometheus.NewDesc("pg_stat_database_{0}", "{2}", nil, nil) }}, """.format(column.strip(), ctype, description.strip())
```
Adjust the value of the resultant prometheus value type appropriately. This helps build
rich self-documenting metrics for the exporter.
