# Logging

The `ansible_run` script detects if it is run in a terminal, or through
the `cron` system. When a terminal is connected, the logging is sent to
the screen, so the user running Ansible can see what happens.

But, when the `ansible_run` script is run through `cron`, the logging
is placed in the `/var/log/ansible` directory. The logfiles are named
`ansible_run_<ansible_environment>_<day_of_month>.log`, e.g.
`ansible_run_svc_09.log`

The `ansible_run` script automatically starts a new logfile when the day
starts, which automatically implies that logging will be kept for one
month.
