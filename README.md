# Meltano Speedrun: from 0 to ELT in 90 seconds

[Meltano](https://meltano.com/) project created in the
["from 0 to ELT in 90 seconds" speedrun](https://meltano.com/blog/2021/04/28/speedrun-from-0-to-elt-in-90-seconds/)
by executing the following commands after
[installing Meltano](https://meltano.com/docs/getting-started.html#install-meltano) and setting up a local PostgreSQL database named `speedrun`:

```py
# First, we initialize a new Meltano project named "speedrun"
meltano init speedrun
# And we move into the newly generated directory
cd speedrun

# Then, we add the GitLab extractor
meltano add extractor tap-gitlab
# And we configure it to pull data from the "meltano/meltano" project
meltano config tap-gitlab set projects meltano/meltano
# And to only go back to April 1st of this year
meltano config tap-gitlab set start_date 2021-04-01T00:00:00Z
# Next, we set it up to only load data from the "tags" stream
meltano select tap-gitlab tags

# And now it's time to add the loader for PostgreSQL, which I have running on my local machine
meltano add loader target-postgres
# And we configure it with the correct username
meltano config target-postgres set postgres_username my-username # TODO: Add the username for your (local) PostgreSQL instance here
# And the correct database name
meltano config target-postgres set postgres_database speedrun

# Now it's time to run the actual ELT pipeline
meltano elt tap-gitlab target-postgres

# And now that extract and load are complete, we will look inside the Postgres database to make sure all the data has made it there
psql -d speedrun
SELECT * FROM tap_gitlab.tags ORDER BY _sdc_received_at;
# And there you go!
```

## Tools

- Meltano: ELT for the DataOps era – https://meltano.com/
- `tap-gitlab`: GitLab extractor – https://meltano.com/plugins/extractors/gitlab.html
- `target-postgres`: PostgreSQL loader – https://meltano.com/plugins/loaders/postgres.html

More sources: https://meltano.com/plugins/extractors/

More destinations: https://meltano.com/plugins/loaders/
