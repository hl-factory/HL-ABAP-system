# Pitfalls

Things that trip people up when importing, running, or changing the demos. For step-by-step fixes, see [Debugging](../how-to-contribute/debugging.md).

## Import and setup

- **Root package name length.** Use 11 characters or fewer. Prefix folder logic appends folder names such as `_NEW_GEN_CDS_VIEWS`.
- **Empty package file.** `src/new_gen_cds_views/package.devc.xml` is 0 bytes. It was emptied by an upload on 13 Jan 2023 and never restored.
- **CDS entity buffer is not in Git.** abapGit cannot serialize DTEB objects, so the buffer on `Z_DEMO_ENTITY_BUFFER` must be created by hand (`README.md`).
- **System type matters.** DDIC-based CDS views (`Z_CLASSIC_VIEW`, `Z_VIEW_EXTENSION`), executable programs (`src/itab_news/`), and classic HTTP and ZIP APIs (`YBW_LOAD_DATA`) probably will not activate in ABAP Cloud. The RAP and test double APIs in `src/test_isolation/` need a recent release.
- **SAP demo content must exist.** `SFLIGHT`, `SPFLI`, `SCARR`, `SAIRPORT`, `DEMO_RENT`, `DEMO_SALES_SO_I`, and `/DMO/I_TRAVEL_M` are not in this repository.

## Data

- **Tables ship empty.** Every `YBW_*` demo returns nothing until `YBW_LOAD_DATA` has run, and the loader does nothing until two `` `Set URL` `` constants are replaced.
- **Unstable URLs.** The loader's own licence hint warns that the Bundeswahlleiter URLs change. The votes URL is hard-coded to `kerg2_00287.csv`, which may already be outdated.
- **Positional CSV parsing.** Columns are mapped by index and header lines are skipped by count. A changed file layout silently loads wrong data instead of failing.
- **No loader for `YDEMOS4_CHAR10` or `YBW_INFRA`.** The char-to-numc demos need manual test data.
- **Demos modify data.** `YBW_TIPPS4` writes fictional parties (ID 1000) into `YBW_PARTY`, `YBW_TIPPS6` inserts a candidate, and the char-to-numc classes delete and refill `YDEMOS4_NUMC10`. Re-run the loader to reset the election tables.

## Inconsistent filter values

The demos filter the `KIND` column (of `YBW_PARTY`, and of `YBW_VOTE` in one view) with two different spellings:

| Value | Used in |
| --- | --- |
| `'Partei'` | `YBW_JOIN`, `YBW_INTERSECT`, `YBW_CTE`, `YBW_VOTES_PARTY` (on `YBW_VOTE-KIND`) |
| `'PARTEI'` | `YBW_TIPPS2`, `YBW_TIPPS3`, `YBW_TIPPS4` |

ABAP SQL comparisons are case-sensitive. If the loaded CSV uses `Partei`, the `ON`-condition filter in `YBW_TIPPS2` and `YBW_TIPPS3` matches no party, so every candidate gets an empty (or `'< None >'`) party name, and the `WHERE` variant in `YBW_TIPPS2` returns no rows at all. That may be intentional for the ON-versus-WHERE comparison, but it is not explained in the comments. The 27 Apr 2022 commit "Update examples to fit dataset" changed the set operation demos but not the tips.

## Paired demos

`YBW_JOIN` asserts equality with `YBW_INTERSECT=>EXECUTE_INTERSECT( )`, and `YBW_WINDOWING4` with `YBW_WINDOWING_ABAP=>READ_VOTES_AGGREGATED( )`. Change both sides together. `YBW_WINDOWING4` and `YBW_WINDOWING_ABAP` both take `UP TO 10 ROWS` ordered only by `votes descending`; with tied vote counts the two queries could pick different rows and the assertion would fail.

## Test isolation

- **Doubles are static.** `ZATI_CL_FACTORY` stores injected doubles in class attributes. The tests call `ZATI_TH_INJECTOR=>inject_depended_on_component( )` but never `ZATI_TH_INJECTOR=>clear( )`, so a double injected in one test class stays active for the rest of the run. It does not break the current tests, but new tests that expect the real component will get the double.
- **The real DOC always fails.** `ZATI_CL_DEPENDED_ON_COMPONENT~add` is `assert 1 = 0`.
- **CUT and DOC are `create private`.** Get instances only through `ZATI_CL_FACTORY`.

## Documentation gaps

- `src/itab_news/` is missing from `README.md`, and `src/itab_news/README.md` is blank.
- `README.md` still lists two RAP sessions as "(planned)".
- The REUSE badge in `README.md` points at the upstream `SAP-samples` repository, not this copy.

## Related pages

- [Background](index.md)
- [Cleanup opportunities](../cleanup-opportunities.md)
