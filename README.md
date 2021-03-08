Ansible Role: restic
=========

Set up backups using restic and runrestic.

Role Variables
--------------

```yaml
restic_version: 0.12.0
runrestic_version: 0.5.20
```

The version of restic and runrestic to be installed.


```yaml
restic_systemd_timer_oncalendar: daily
```

The time and date specifications when create and prune will run. See [systemd documentation](https://www.freedesktop.org/software/systemd/man/systemd.time.html) for possible values.


### runrestic configuration

#### Required Arguments

`restic_repository`: Path to a [repository](https://restic.readthedocs.io/en/stable/030_preparing_a_new_repo.html). Can be a list if you want to backup to multiple repositories.

`restic_password`: Password for the repository.

`restic_source_directories`: List of local folders to back up.

#### Optional Arguments

`restic_backup_name: restic backup` - Name for the backup

`restic_execution_parallel: true` - Parallel backup creation
`restic_execution_retry_count: 10` - Retry count
`restic_execution_retry_backoff: 1:00 exponential` - Retry strategies (static, linear, exponential)

`restic_environment_variables: []` - Environment variables for restic, see [restic documentation](https://restic.readthedocs.io/en/stable/040_backup.html#environment-variables) for all available options

`restic_exclude_patterns: []` - Patterns to exclude from backup
`restic_exclude_files: []` - Files to exclude from backup

`restic_pre_hooks: []` - Run command before the backup
`restic_post_hooks: []` - Run command after the backup

```yaml
restic_retention_policy:
  keep_last: 3
  keep_daily: 7
  keep_weekly: 4
  keep_monthly: 6
```

Retention policy for how many backups to keep in each category (daily, weekly, monthly, etc).

```yaml
restic_checks: []
```

List of consistency checks.

```yaml
restic_metrics_prometheus: false
restic_metrics_prometheus_path: /var/lib/node_exporter/textfile_collector/runrestic.prom
```

Prometheus metrics for node_exporter textfile collector.

`restic_metrics_prometheus_password_replacement: XXX` - Replace passwords from repositories in metrics

Example Playbook
----------------

### Local backup

    - hosts: servers
      vars:
        restic_repository: /srv/restic-repo
        restic_password: changeme
        restic_source_directories:
          - /var/www
          - /etc
      roles:
         - { role: outwire.restic }


### S3 backup

    - hosts: servers
      vars:
        restic_repository: s3:s3.example.com/bucket123/backup
        restic_password: changeme
        restic_environment_variables:
          AWS_ACCESS_KEY_ID: changeme_access_key
          AWS_SECRET_ACCESS_KEY: changeme_secret_key
        restic_source_directories:
          - /var/www
          - /etc
      roles:
         - { role: outwire.restic }


License
-------

MIT
