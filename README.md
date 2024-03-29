# Developer guide
How to use this repository guide:

1. Read the general advice on this page. Then refer to the following as needed.
2. Contribute to this documentation by creating an issue

## Global git ignore
[Set up global git ignore](https://stackoverflow.com/questions/36732119/how-to-setup-gitignore-for-windows) for your machine-specific files and folders, such as configurations generated by your IDE (e.g. \*.idea)

## Python virtual environment
Use a virtual environment installer

### Code cleaning tools
Black, isort, pylint

### Document first

A quick, a simple markdown file in repositories go a long way. Update the
documentation alongside the code to avoid the "what was I doing here again?".

The audience of these readme's are developers, but explain what the code is
supposed to accomplish in plain language.

### Solve the problem without making the code complex

For example, if the task is to load `csv` data from an S3 bucket into a 
BigQuery table then start with just that until it needs to do more:

```shell
gsutil -m cp -r s3://src gs://target
bq load ds.new_tbl gs://target/*.csv ./schema.json
```
Learning to balance simple and robust is a skill, but peer reviews will help.

### Let patterns and abstractions emerge organically

Take it as "Early Abstraction is bad".

Don't try to create three levels of abstraction all in one go, but instead
let them develop naturally overtime. When you start to duplicate functions
or see patterns, thenm at _that_ time, figure out how to abstract.

### Configuration is for environments not logic

For configurations, the custom environment variables should generally
be coded in the lines as they are used. It's easier for the reader to
see the environment variable inline versus having to remember it or
scrolling up and down the file.

In the example, we only need to configure the server since that is the
only environment specific thing.

> Bad

```python
db_server = os.getenv('DB')
db_schema = os.getenv('DB_SCHEMA')
tables = os.getenv('DB_TABLES')

conn = connect_to_db(db_server, db_schema)

for i in tables:
    do_thing_with(conn, i)
```

> Good

```python
db_server = os.getenv('DB')
db_schema = 'database_i_want'
tables = ['table_1', 'table_2', ...]

conn = connect_to_db(db_server, db_schema)

for i in tables:
    do_thing_with(conn, i)
```

### Embrace the YAGNI

**Y**ou **A**int **G**onna **N**eed **I**t

If you find yourself adding code for "Just in case..." or "What if..." STOP!

Get the code out for the cases in front of you right now... IF that
'what if' case comes up at some point in the future, we'll write the code to
fix it then.

### Test or go home

Building on the last point. You can use whatever technology makes the most
sense as long as you can unit test it to an acceptable degree. For example if
you're writing a shell script for the pipeline then you could use
https://github.com/bats-core/bats-core to write tests as an example.

But "What's an acceptable degree?" Well, it's case by case, but branch coverage
is a good indicator, but not the only one for example.

Generally if you're unsure about a thing, add a test.

### Fail fast

If an endpoint is finicky or unreliable, and you need to ensure that it
works "hand-offs" worked, then test that before continuing.

The sooner we know we have a problem the sooner we can: fix it, test the fix,
and re-deploy it
