# This is a soft fork of daranzolin/rcanvas

`master` here carries fixes and features that are also open as PRs upstream. It
is meant to be installed from directly:

```r
devtools::install_github("bshor/rcanvas")
```

Upstream is not dead — PR #73 was opened and merged the same day, by
@pachadotdev — so the intent is to keep everything upstreamable rather than to
diverge. This branch exists so the teaching pipeline has a stable install target
while PRs are in flight.

## What master carries beyond upstream

| Change | Upstream PR | Status |
|---|---|---|
| Fix `delete_wpage()`: `make_canvas_url()` instead of `paste0()`, and import `httr::DELETE` | [#74](https://github.com/daranzolin/rcanvas/pull/74) | open |
| Add `delete_assignment()` / `delete_assignments()` | [#75](https://github.com/daranzolin/rcanvas/pull/75) | open |

`get_course_gradebook()` rewrite ([#73](https://github.com/daranzolin/rcanvas/pull/73))
is already merged upstream and is not a divergence.

## Workflow for adding a feature

1. Branch off `master`: `git checkout -b feat/thing`
2. Write the function, add roxygen docs, then `devtools::document()`
3. **Revert unrelated roxygen churn** — regenerating docs tends to touch
   `DESCRIPTION` (RoxygenNote), `man/rcanvas.Rd`, and unrelated `.Rd` files.
   Keep the diff to your function and its docs.
4. Test against a live Canvas instance. For destructive verbs, create a
   throwaway object, act on it, and confirm — do not test on real course content.
5. Push the branch and open a PR upstream against `daranzolin/rcanvas:master`
6. Merge the branch into this fork's `master` with `--no-ff`
7. Reinstall: `devtools::install_github("bshor/rcanvas")`

## If upstream merges a PR

Rebase or merge upstream into `master` and drop the duplicated commit. Since
`master` diverges, syncing is a merge, not a fast-forward:

```r
git remote add upstream https://github.com/daranzolin/rcanvas.git   # once
git fetch upstream && git merge upstream/master
```

Once everything here is upstream, this file and the divergence go away, and the
install line goes back to `daranzolin/rcanvas`.

## Known upstream bugs not yet fixed here

- [#50](https://github.com/daranzolin/rcanvas/issues/50) / [#70](https://github.com/daranzolin/rcanvas/issues/70) —
  the same `canvas_url()` slash bug in other call sites. [#65](https://github.com/daranzolin/rcanvas/pull/65)
  (open since 2023) fixes most of them; ours covers `pages.R` only.
- [#68](https://github.com/daranzolin/rcanvas/issues/68) — `get_course_gradebook()`
  is slow: it pages submissions per assignment. 311 seconds for 679 students and
  40 assignments. Canvas's `/students/submissions` endpoint with
  `student_ids[]=all&grouped=true` would cut this substantially.
- [#66](https://github.com/daranzolin/rcanvas/issues/66) — no wrapper for sending
  Canvas Inbox messages.

See `../RCANVAS.md` for how this fits into the course pipeline.
