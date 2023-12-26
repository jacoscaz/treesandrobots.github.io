---
title: "Simple log management by piping logs to syslog"
description: "A simple, underestimated approach to log management"
---

A very simple way to persist the logs of tasks run via crontab is to pipe
`stdout` and `stderr` into `/usr/bin/logger`, which merges them into the 
machine's [syslog][0]. This reuses the existing logging infrastructure
already present on most UNIX-y machines, including standard log rotation.
Best suited for small amounts of lines.

`<some-command> 2>&1 | /usr/bin/logger -t <tag>`

[0]: https://en.wikipedia.org/wiki/Syslog
