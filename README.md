# ops-uptime

External uptime check for Brookbase. It calls the worker health endpoint every 5 minutes from GitHub Actions, outside the production server.

- The URL is stored in the repository secret `HEALTH_URL` (`https://crm.brookbase.online/api/health/worker`).
- Only status 200 is healthy. Any other status, a timeout (20 seconds) or no connection is a failure.
- At the second failure in a row the workflow opens an issue titled "Uptime alarm: the health check is failing" and mentions the repository owner, so GitHub sends an email. When the endpoint answers 200 again, the workflow closes the issue with a comment, which sends a second email. The run also turns red while the endpoint fails.
- Each run checks 10 times over about 45 minutes and then starts the next run with `workflow_dispatch`. It passes the number of failures in a row on to the next run.
- The cron line in `uptime.yml` is a fallback. GitHub drops most scheduled runs, but one that does run restarts the chain if it broke.
- `keepalive.yml` commits once a month, because GitHub disables scheduled workflows in public repositories after 60 days without activity.

## Stop and start

- Disable the Uptime workflow under Actions to stop it. Cancelling a run is not enough, because a waiting scheduled run continues the chain.
- Enable the workflow and start it with Run workflow to resume.

## Limits

- The repository must stay public. Private repositories have a monthly minute quota that a check running around the clock would use up.
- Issues and run logs are public. They show status codes and times, never the URL.
- GitHub's terms do not allow Actions on GitHub-hosted runners for activity unrelated to the software project in the repository, or activity that puts a disproportionate burden on its servers. A workflow that runs around the clock is a grey area. If GitHub objects, move the check to a dedicated uptime service.
