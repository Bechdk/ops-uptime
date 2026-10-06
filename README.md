# ops-uptime

External uptime check for OperationsCRM. GitHub Actions calls the health endpoint four times an hour and fails the run when it does not answer with 2xx three times in a row. GitHub then emails the user who last changed the cron line in `uptime.yml`.

- The URL is stored in the repository secret `HEALTH_URL`.
- `keepalive.yml` commits once a month, because GitHub disables scheduled workflows in public repositories after 60 days without activity.
- GitHub can delay scheduled runs and drop some under high load.
