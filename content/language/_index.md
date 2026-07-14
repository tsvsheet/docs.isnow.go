---
title: Language tour
---

Every isnow concept has one name. This page is the working vocabulary; the normative definitions live in [SPECIFICATION.md](https://github.com/uplang/isnow/blob/main/SPECIFICATION.md).

## The membership test

An **isnow** *holds at* an **instant** when every field constraint is satisfied. That test — `Holds(at)` — is the language's defining operation; "next occurrence" and "previous occurrence" are derived from it. An **occurrence** is any instant at which the isnow holds.

```go
p, _ := isnow.Parse("M,W,F noon")
p.Holds(time.Now())          // is it now?
p.Next(time.Now())           // the next occurrence
```

## The seven fields

An isnow is seven **fields** in three **groups**:

```
Y/m/d   w   H:M:S      [ >|>= spec ]   [ <|<= spec ]
└ date group ┘ │ └ time group ┘
          bare group
```

| Field | Group | Separator |
| --- | --- | --- |
| year, month, day | date group | `/` |
| weekday | bare group | — |
| hour, minute, second | time group | `:` |

**Groups** are separated by whitespace, `.`, or `_`, so an isnow is a single shell-safe token: `Y/m/d.w.H:M:S` ≡ `Y/m/d_w_H:M:S` ≡ `Y/m/d w H:M:S`.

## The field algebra

One uniform algebra applies to every field:

| Construct | Name | Example |
| --- | --- | --- |
| `*` | wildcard | `*` |
| `12` | exact value | hour 12 |
| `M,W,F` | set | Mon, Wed, Fri |
| `!1` | exclusion | not the 1st |
| `8-12` | span (inclusive, wraps on cyclic fields) | hours 8–12 |
| `-1` | from-end value | the last day of the month |
| `-2w1d` | unit compound | the last 2 weeks and 1 day |
| `+[15]` | step from an anchor | `:0+[15]` = every 15 minutes |
| `-[1]` | step from the end | `Th-[1]` = the last Thursday |
| `Monday+[3]` | weekday-occurrence step | the 3rd Monday of the month |

**Symbols** are case-insensitive, minimal-unique names: weekdays `Su M Tu W Th F Sa` (plus runs `MWF`, `SS`, `TT`), and the times `noon`/`midday` (12:00:00) and `midnight` (00:00:00). `m` is always Monday.

## Canonical form and the shorthand ladder

The **canonical form** is the fully-qualified `Y/m/d w H:M:S` expansion of any isnow. The **shorthand ladder** lets a short isnow stand for its canonical form by position:

| Shorthand | Canonical |
| --- | --- |
| `6` | `*/*/* * 06:00:00` |
| `M noon` | `*/*/* Monday 12:00:00` |
| `/1 18` | `*/*/01 * 18:00:00` |
| `Su :0,30` | `*/*/* Sunday *:00,30:00` |

Produce it with `isnow canon <isnow>` or `Pattern.Canonical()`.

## Bounds

A **since bound** (`>`, `>=`) and an **until bound** (`<`, `<=`) each carry a sub-spec and define a **window**:

```
12 <9/1                 every day at noon until September 1
::+[9] >=6 <=18         every 9 seconds from 06:00 to 18:00
```

> **v0.1.0 note:** steps are field-local in every context; a step counting continuously across a bounded window is a planned future extension. Bounds themselves are fully honored.
