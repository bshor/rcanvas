# Historical soft fork of daranzolin/rcanvas

This fork temporarily carried fixes while their upstream PRs were open. All of
those changes were merged upstream on 2026-08-18, so install the canonical
repository directly:

```r
devtools::install_github("daranzolin/rcanvas")
```

The fork remains as development history and may host future PR branches, but its
`master` branch is not the package installation target.

## Changes formerly carried ahead of upstream

| Change | Upstream PR | Status |
|---|---|---|
| Fix `delete_wpage()`: `make_canvas_url()` instead of `paste0()`, and import `httr::DELETE` | [#74](https://github.com/daranzolin/rcanvas/pull/74) | merged |
| Add `delete_assignment()` / `delete_assignments()` | [#75](https://github.com/daranzolin/rcanvas/pull/75) | merged |
| Fix `get_announcements()` and add announcement create/update/delete support | [#76](https://github.com/daranzolin/rcanvas/pull/76) | merged |

`get_course_gradebook()` rewrite ([#73](https://github.com/daranzolin/rcanvas/pull/73))
is already merged upstream and is not a divergence.

## Workflow for adding a feature

1. Fetch upstream and branch from `upstream/master`:
   `git checkout -b feat/thing upstream/master`
2. Write the function, add roxygen docs, then `devtools::document()`
3. **Revert unrelated roxygen churn** — regenerating docs tends to touch
   `DESCRIPTION` (RoxygenNote), `man/rcanvas.Rd`, and unrelated `.Rd` files.
   Keep the diff to your function and its docs.
4. Test against a live Canvas instance. For destructive verbs, create a
   throwaway object, act on it, and confirm — do not test on real course content.
5. Push the branch and open a PR upstream against `daranzolin/rcanvas:master`.
6. Merge only after the PR is clean and the relevant checks pass. Preserve the
   repository's merge-commit style, especially for stacked PRs.
7. Reinstall: `devtools::install_github("daranzolin/rcanvas")`.

## Remaining upstream issues

- [#50](https://github.com/daranzolin/rcanvas/issues/50) — the same
  `canvas_url()` slash bug in other call sites. [#65](https://github.com/daranzolin/rcanvas/pull/65)
  was repaired, tested, and merged on 2026-08-18 for group and enrollment
  endpoints; page, course, migration, and utility call sites remain. #74 covers
  `delete_wpage()`, and #76 fixes the announcements call identified in
  [#70](https://github.com/daranzolin/rcanvas/issues/70).
- [#68](https://github.com/daranzolin/rcanvas/issues/68) — `get_course_gradebook()`
  is slow: it pages submissions per assignment. 311 seconds for 679 students and
  40 assignments. Canvas's `/students/submissions` endpoint with
  `student_ids[]=all&grouped=true` would cut this substantially.
- [#66](https://github.com/daranzolin/rcanvas/issues/66) — no wrapper for sending
  Canvas Inbox messages.

See `../RCANVAS.md` for how this fits into the course pipeline.
