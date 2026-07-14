---
title: isnow.go
---

**isnow** is a date/time *pattern* language — formally *DTimpalr, a Date/Time Pattern Language for Repetition*. An **isnow** (the language, and a pattern string in it) describes anything from a fixed instant to a complex recurrence and answers one question: **is it now?**

`isnow.go` is the Go implementation: an importable library, the `isnow` command-line tool, and an HTTP time server. It is a strict superset of cron in expressiveness — sets, spans, exclusions, from-end counting, steps, and since/until bounds, over one uniform per-field algebra.

```
6                       every day at 06:00
M,W,F noon              Mon/Wed/Fri at 12:00:00
11/ Th-[1] noon         the last Thursday of November at noon
::+[9] >=6 <=18         every 9 seconds from 06:00 to 18:00
```

- **[Language tour](language/)** — the terminology, the seven fields, the algebra, and the shorthand ladder.
- **[CLI reference](cli/)** — every `isnow` command with runnable examples.
- **[HTTP API reference](http/)** — the endpoints `isnow serve` exposes.
- **[Migrating from cron](cron/)** — your crontab, as isnows.

The language itself (grammar, specification, and the conformance corpus every implementation passes) lives in [uplang/isnow](https://github.com/uplang/isnow). The library is `github.com/uplang/isnow.go`.
