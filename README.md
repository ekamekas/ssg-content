# Content Repository

This repository acts as a content management system for static-site generators
such as Jekyll, Next.js, Hugo, and others.

Content is written in markdown with front matter kept as SSG-agnostic as it
reasonably can be.

## Structure

Writing is split by rubrik. A post's directory becomes its URL, so a file at
`engineering/foo.md` is published at `/engineering/foo/`.

| Directory | Holds | Belongs here when |
| --- | --- | --- |
| `engineering/` | Technical notes, learning journals, benchmarks, architecture. | There is code, architecture, or a number in it. |
| `ulasan/` | Books, tools, courses, conferences. | There is a clear object being assessed. |
| `catatan/` | Essays and reflections outside of work. | Everything else. |
| `static/` | Diagrams and files referenced by posts. Published at the site root. | — |

## Front matter

```toml
+++
title       = 'Java 25'          # no rubrik prefix: the directory already says it
description = '...'              # shown on list pages and in the OG card
date        = 2025-10-29
draft       = false
series      = ['Engineer Journal']   # optional, only for genuine multi-part runs
tags        = ['java']               # subject only, never rubrik or series names
aliases     = ['/posts/<old-slug>/'] # only on posts that moved
+++
```

Three conventions worth keeping:

- **No rubrik prefix in `title`.** Titles used to carry one (`Engineer Journal -
  Java 25`) because every post lived under `/posts/`. The directory carries it
  now, so the prefix is only repetition that eats title width in every list.
- **`series` is not `tags`.** A series is a run that continues (`Learn Zig #1`
  implies a `#2`); it gets its own page and prev/next navigation. Tags are
  subjects.
- **`aliases` stays forever** on posts that moved out of `/posts/`. Removing one
  breaks every link that was ever shared.
