# pandas Pre-Upgrade Audit

**Audited repository:** [pandas-dev/pandas](https://github.com/pandas-dev/pandas)
**Audit date:** 2026-10-25 (all data pulled live from the GitHub API at audit time)
**Purpose:** pre-upgrade note for the research group's pandas dependency bump
**Prepared by:** HeziLiu

---

## 1. Where the DataFrame class is actually defined

The `DataFrame` class is defined in **`pandas/core/frame.py`** in the pandas source tree.

**How it was located:** by searching the `pandas-dev/pandas` code for the DataFrame class definition and then pulling the file contents directly from the repository at both the default branch and the release tag. (A naive `class DataFrame` search also surfaces the interchange-protocol code in `pandas/core/interchange/` — those are the *dataframe interchange protocol* implementations, not the primary class. The primary class lives in `pandas/core/frame.py`.)

### On the development branch

- **File:** `pandas/core/frame.py`
- **Branch / ref:** `main` — the repository's default branch — at commit `199f52b12cbb470118bbafe3f4b95f750a78c24e` (the tip of `main` when the file was pulled)
- **File blob SHA:** `c99e26fefc8b477d792afa2d2272d05e4fcd6ccb`

The class definition line, quoted from that source (decorator shown for context):

```python
@set_module("pandas")
class DataFrame(NDFrame, OpsMixin):
```

The docstring immediately following begins:

```python
    """
    Two-dimensional, size-mutable, potentially heterogeneous tabular data.

    Data structure also contains labeled axes (rows and columns).
```

So on the development branch, `DataFrame` inherits from `NDFrame` (the generic labeled-array base) and `OpsMixin`, and is re-exported under the `pandas` module namespace via `@set_module("pandas")`.

### Under the newest release tag (`v3.1.0`)

The same file **does exist** under the newest release tag:

- **Tag:** `v3.1.0` — the annotated tag object points to commit `c6f46769b808eb60218189d089dd67841b44f1b9`
- **File:** `pandas/core/frame.py` at that commit
- **File blob SHA:** `c99e26fefc8b477d792afa2d2272d05e4fcd6ccb`

The class definition line under the release tag, quoted from that source:

```python
@set_module("pandas")
class DataFrame(NDFrame, OpsMixin):
```

**Note for the team:** the blob SHA is *identical* at both refs, i.e. `pandas/core/frame.py` is byte-for-byte unchanged between the `v3.1.0` release and the current tip of `main`. The class definition we cite from the development branch is therefore exactly the code that ships in the release we would actually run.

---

## 2. Release state

- **Most recently cut release:** **`v3.1.0`** — "pandas 3.1.0", a stable release (not a draft, not a prerelease).
  - Release published: **2026-10-22 13:20 UTC** (release object created 2026-10-22 11:22 UTC).
  - Release notes highlights: a new `DataFrame.select()` method with an updated `DataFrame.filter()`, and faster `pd.read_csv()` with parallel reads of large files.
- **Tag details for `v3.1.0`:**
  - Annotated tag object SHA: `e7e82e333559f162c67e5c8c5dad25c113bd6f8c`, message "Version 3.1.0", tagged by Joris Van den Bossche on **2026-10-22 11:22 UTC** (unsigned).
  - **The tag points to commit:** `c6f46769b808eb60218189d089dd67841b44f1b9`
- For completeness: the newest *tag* in the repository overall is `v3.2.0.dev0` (a development placeholder pointing at `31330b5ea87023f8361e94811c3cbee75a96accc`), which is **not** a cut release. The most recent actual releases before `v3.1.0` were `v3.1.0rc0` (2026-09-30) and the `v3.0.x` patch line, latest `v3.0.6`.
- An active maintenance branch `3.1.x` exists (currently at `9e06cdb761f817c78d0e6ba73e3ab795636d2f0e`), so patch releases on the 3.1 line are expected.

**Upgrade implication:** bumping to "the newest release" today means **pandas 3.1.0**, cut roughly three days before this audit.

---

## 3. Security hygiene (code scanning alerts)

**Code scanning alerts for `pandas-dev/pandas` are not accessible with the credentials used for this audit.** The GitHub API returns:

```
GET /repos/pandas-dev/pandas/code-scanning/alerts: 403 Resource not accessible by personal access token
```

This was the case for both the *open*-alerts query and the *closed / resolved / dismissed*-alerts query. Therefore I can report neither the three most recent open alerts (rule / severity / location) nor the three most recently resolved or dismissed ones. **This is an access limitation on the auditor's side — it is not a statement that the project has zero alerts.**

What can still be verified without the alert data:

- pandas runs automated security tooling in CI: a **CodeQL** workflow (`.github/workflows/codeql.yml`, active since 2022) and a dedicated **"GitHub Actions Security Analysis with zizmor"** workflow (`.github/workflows/security.yml`, active since 2026-05).
- Nothing in the data pulled for this audit (releases, tags, CI runs) indicates a published security advisory blocking the upgrade.

**Recommendation:** if upstream security posture is required for our sign-off, re-check the repository's Security tab in the GitHub UI, or re-run this check with a token that has security-events read scope on `pandas-dev/pandas`.

---

## 4. CI health: newest completed run of the test matrix on the default branch

**Workflow:** "Unit Tests" (`.github/workflows/unit-tests.yml`) — the workflow that runs pandas' test matrix.

**Newest completed run on `main`:**

| Field | Value |
|---|---|
| Run ID | `40145282607` (run number 45519, attempt 1) |
| Branch | `main` (the default branch) |
| Head commit | `199f52b12cbb470118bbafe3f4b95f750a78c24e` |
| Trigger | `push` |
| Commit title | "ENH: show the data in Styler's text repr (GH#55908) (#71499)" |
| Started | 2026-10-25 00:04:50 UTC |
| Completed | 2026-10-25 00:58:05 UTC (~53 minutes wall clock) |
| **Overall conclusion** | **success** |

**Failed jobs: none.** A failed-jobs query across the run returned **0 failed jobs** ("No failed jobs found in this workflow run"). Because nothing failed, there is no failed-job error output to quote; the excerpt below is from a successful job.

### Platforms and Python versions covered by this run

The run's job listing (latest attempt) totals **39 jobs, all successful**, breaking down as follows.

**Core matrix — 5 platforms × 4 Python versions (20 jobs):**

| Platform (runner) | Python 3.11 | Python 3.12 | Python 3.13 | Python 3.14 |
|---|---|---|---|---|
| ubuntu-24.04 (x86_64) | ✓ | ✓ | ✓ | ✓ |
| ubuntu-24.04-arm (aarch64) | ✓ | ✓ | ✓ | ✓ |
| macos-15 (Apple Silicon) | ✓ | ✓ | ✓ | ✓ |
| macos-15-intel (x86_64) | ✓ | ✓ | ✓ | ✓ |
| windows-2025 | ✓ | ✓ | ✓ | ✓ |

**CPython development builds (4 jobs):** `python-dev` (nightly CPython) on ubuntu-24.04, macos-15, macos-15-intel and windows-2025.

**Special-configuration jobs (15):** Clang (ASAN/UBSAN), Linux-32-bit, Linux-Musl, Pyodide build, Windows-MinGW, Locale: it_IT, Locale: zh_CN, Without PyArrow, Numpy Nightly, Pyarrow Nightly, Freethreading, Minimum Versions, Downstream Compat, Future configs (legacy values), Future configs (upcoming values).

### Representative job log excerpt

From the successful job **`windows-2025 py311`** (job ID `121025180554`), which ran the full Windows test suite in ~38 minutes:

```text
2026-10-25T00:09:57.9622810Z [1/1] Generating write_version_file with a custom command
2026-10-25T00:09:57.9647288Z INFO: autodetecting backend as ninja
2026-10-25T00:09:57.9648256Z INFO: calculating backend command to run: D:\a\pandas\pandas\.pixi\envs\py311\Library\bin\ninja.exe
2026-10-25T00:11:46.2017523Z ============================= test session starts =============================
2026-10-25T00:11:46.2018844Z platform win32 -- Python 3.11.17, pytest-9.1.1, pluggy-1.7.0
2026-10-25T00:11:46.2019793Z PySide6 6.11.2 -- Qt runtime 6.11.2 -- Qt compiled 6.11.2
2026-10-25T00:11:46.2020619Z rootdir: D:\a\pandas\pandas
2026-10-25T00:11:46.2021319Z configfile: pyproject.toml
2026-10-25T00:11:46.2022069Z plugins: cov-7.1.0, localserver-0.0.0, qt-4.5.0, xdist-3.8.0
2026-10-25T00:11:46.2022892Z created: 2/2 workers
2026-10-25T00:11:46.2023469Z 2 workers [256329 items]
2026-10-25T00:11:46.2023900Z
2026-10-25T00:47:53Z  ..........s.s.s.ss..................sssssssssss..ssssssssss.ss...
                  [ ~37 minutes of pytest progress output elided here;
                    '.' = passed, 's' = skipped, 'x' = xfailed ]
2026-10-25T00:47:55.2785237Z 2.53s call     pandas/tests/io/formats/style/test_html.py::test_cellstyle_rules_split_at_8192_selectors[map_index_0]
2026-10-25T00:47:55.2786251Z 2.52s call     pandas/tests/io/formats/style/test_html.py::test_cellstyle_rules_split_at_8192_selectors[map]
2026-10-25T00:47:55.2787020Z 2.19s call     pandas/tests/frame/test_reductions.py::test_reduction_axis_none_returns_scalar[float64-True-skew]
2026-10-25T00:47:55.2787805Z 2.12s call     pandas/tests/frame/methods/test_cov_corr.py::TestDataFrameCorr::test_corr_scipy_method[kendall]
2026-10-25T00:47:55.2790603Z 1.88s call     pandas/tests/frame/methods/test_info.py::test_info_verbose_with_counts_spacing[...]
2026-10-25T00:47:55.2791371Z 1.48s call     pandas/tests/io/parser/test_parallel_read.py::test_row_blocked_conversion_leading_overflow[6]
2026-10-25T00:47:55.2792113Z 1.44s call     pandas/tests/io/parser/test_parallel_read.py::test_row_blocked_conversion_leading_overflow[1]
2026-10-25T00:47:55.2792836Z 1.42s call     pandas/tests/io/parser/test_parallel_read.py::test_row_blocked_conversion_late_dtype_miss[6]
2026-10-25T00:47:55.2793597Z 1.34s call     pandas/tests/io/parser/test_parallel_read.py::test_row_blocked_conversion_late_dtype_miss[1]
2026-10-25T00:47:55.2794359Z 1.31s call     pandas/tests/io/parser/test_parallel_read.py::test_row_blocked_conversion_matches_python_engine[6]
2026-10-25T00:47:55.2795079Z 1.27s call     pandas/tests/io/parser/test_parallel_read.py::test_row_blocked_conversion_matches_python_engine[1]
2026-10-25T00:47:55.2795786Z 1.16s call     pandas/tests/plotting/test_misc.py::TestDataFramePlots::test_scatter_matrix_axis[True]
2026-10-25T00:47:55.2796514Z 1.13s call     pandas/tests/plotting/frame/test_frame.py::TestDataFramePlots::test_errorbar_timeseries[bar]
2026-10-25T00:47:55.2797370Z 1.12s call     pandas/tests/frame/test_stack_unstack.py::TestStackUnstackMultiLevel::test_stack[False]
2026-10-25T00:47:55.2798746Z 1.06s call     pandas/tests/plotting/frame/test_frame.py::TestDataFramePlots::test_errorbar_timeseries[barh]
2026-10-25T00:47:55.2800115Z 1.05s call     pandas/tests/plotting/frame/test_frame_subplots.py::TestDataFramePlotsSubplots::test_subplots_ts_share_axes
2026-10-25T00:47:55.2801496Z 1.00s call     pandas/tests/io/test_pickle.py::test_round_trip_current[cat-expected39-python_pickler-python]
2026-10-25T00:47:55.2802564Z ======= 247244 passed, 7774 skipped, 1311 xfailed in 2263.49s (0:37:43) =======
2026-10-25T00:48:03.3659696Z ##[end-action id=__self_2.__run;outcome=success;conclusion=success;duration_ms=2289804]
2026-10-25T00:48:03.4163273Z Post job cleanup.
```

For comparison, the sanitizer job on the same run (`Clang (ASAN/UBSAN)`, job ID `121025180348`) reported `platform linux -- Python 3.13.16, pytest-9.1.1, pluggy-1.7.0` with `2 workers [225275 items]`, and also passed.

**CI verdict:** the default branch is green. The most recent full test-matrix run succeeded across all 39 jobs — 5 OS/CPU platforms, Python 3.11 through 3.14, plus CPython nightly dev builds — with zero failures; the representative Windows/Python 3.11 job alone passed 247,244 tests.

---

## 5. Access note: organization teams for this account

The GitHub account used for this audit (**HeziLiu**) belongs to **one organization**:

- **`mcpmark-eval-liuhezi`** — the account is a member of this organization, but is **not on any team within it** (the team-membership list for this org is empty).

No other organization memberships are visible to this account, and there is no university org beyond the one listed above. If membership in another organization was expected, it does not exist (or is not visible) for this account.

---

## Summary for the team

1. **`DataFrame` is defined in `pandas/core/frame.py`** as `class DataFrame(NDFrame, OpsMixin):` — identical on `main` and in the `v3.1.0` release tag (the file is unchanged between the two).
2. **Newest release: `v3.1.0`**, tagged 2026-10-22 (11:22 UTC; release published 13:20 UTC), pointing to commit `c6f46769b808eb60218189d089dd67841b44f1b9`.
3. **Code scanning alerts could not be read** with the current token (HTTP 403 on both open and closed alert queries) — re-check via the GitHub UI or a security-scoped token if this is needed for sign-off.
4. **CI is healthy:** the newest completed Unit Tests run on `main` (run number 45519, 2026-10-25) passed all 39 jobs — ubuntu/macos/windows across x86_64, ARM and Intel runners, Python 3.11–3.14 plus nightly — with no failures.
5. **Account access:** member of org `mcpmark-eval-liuhezi`, on no teams within it.

*Report generated from the GitHub API on 2026-10-25. All quoted material is verbatim from the referenced sources; elisions in the log excerpt are marked.*
