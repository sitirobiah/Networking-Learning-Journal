Base config for each router (hostname + interface addresses).
The root password line is intentionally removed. Never commit passwords.
After finishing the phase, you may replace these files with the output of
`show configuration | display set | no-more` (remove any `root-authentication` lines first).
