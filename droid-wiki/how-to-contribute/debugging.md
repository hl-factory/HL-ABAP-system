# Debugging

There are no logs or monitoring here: everything runs interactively in an SAP system. Problems fall into three groups: import problems, missing data or dependencies, and runtime errors, some of which are intentional parts of a demo.

## Import problems

| Symptom | Likely cause | Fix |
| --- | --- | --- |
| abapGit refuses to create subpackages, or names are too long | Root package name longer than 11 characters. `.abapgit.xml` uses `PREFIX` folder logic, so subpackages are `<root>_<FOLDER>`. | Recreate the root package with a shorter name, for example `Z_DSAG01`. |
| `new_gen_cds_views` package has no description or abapGit reports a problem with its package file | `src/new_gen_cds_views/package.devc.xml` is a 0-byte file | Create the package by hand, or restore the file contents from commit `319d533` (`git show 319d533:src/new_gen_cds_views/package.devc.xml`). |
| CDS entity buffer missing | abapGit does not support object type DTEB | Create it manually as described in `README.md` and [Getting started](../overview/getting-started.md). |
| Objects in `src/test_isolation/` do not activate | Missing `/DMO/I_TRAVEL_M`, `DEMO_SALES_SO_I`, or a release without `FINAL( )` or the RAP test double classes | Install the ABAP Flight Reference Scenario and use a recent release. |
| CDS views in `src/new_gen_cds_views/` do not activate | Missing flight model tables (`SFLIGHT`, `SPFLI`, `SCARR`, `SAIRPORT`) or `DEMO_RENT`; or DDIC-based views are not allowed (ABAP Cloud) | Use a system with the flight model; skip `Z_CLASSIC_VIEW` and `Z_VIEW_EXTENSION` on ABAP Cloud. |
| Access control warning on activation | `@AccessControl.authorizationCheck: #CHECK` on views without a DCL | Expected; the repo has no DCL objects. |

## Empty results in the SQL demos

Nearly every `YBW_*` demo returns nothing until the tables are loaded.

1. Run `YBW_LOAD_DATA` (`src/art_data_access/ybw_load_data.clas.abap`). If it only prints the "Dear User" licence text, the URL constants still contain `` `Set URL` ``.
2. If it dumps, the dump text shows the HTTP error message (`assert fields rmsg condition sy-subrc = 0`). Common causes: the URL moved (the licence hint warns that "The URLs for the CSV-files are not stable"), no outbound internet, or missing SSL certificates for `bundeswahlleiter.de`.
3. If the load succeeds but columns look shifted, the CSV layout changed. Update the column indexes and skipped header lines in `load_votes`, `load_parties`, `load_candidates`.

The char-to-numc demos read `YDEMOS4_CHAR10`, which no loader fills. Insert a few rows by hand, including values with leading spaces, to see the difference between `YBW_CHAR_TO_NUMC_3` and `_4`.

## Intentional failures

Some demos are built to fail or to look wrong. Do not "fix" these without reading the comments.

| Object | What happens | Why |
| --- | --- | --- |
| `YBW_TIPPS6` (`src/art_data_access/ybw_tipps6.clas.abap`) | Runtime error `DBSQL_INVALID_CURSOR` | `ybw_candidate_manager=>add_candidate( )` runs the macro `wait-for-db` (`WAIT UP TO 1 SECONDS`, in `src/art_data_access/ybw_candidate_manager.clas.macros.abap`). `WAIT` causes an implicit database commit, which closes the open cursor before `FETCH`. The comment suggests analyzing it with ST05 and ADT trace points. |
| `YBW_TIPPS6` | The inserted last name "asap" would not match the cursor's `as%` pattern | The constant `asap` starts with a Cyrillic `а` (U+0430), not a Latin `a`. The comment says to use the debugger's hex view. |
| `YBW_CTE` | Empty result | The query computes the symmetric difference of two queries that return the same rows. The comment invites the reader to work out why. |
| `YBW_TIPPS5` | Syntax warnings | Uses old ABAP SQL syntax on purpose to compare check strictness with the new syntax. |
| `YBW_CHAR_TO_NUMC_2` | Only prints an empty table | The `INSERT ... FROM SELECT` without a conversion is commented out. |
| `ZDEMO_ITAB_GROUP_BY` | `AT` section shows repeated group keys | `SORT` before `AT END OF` is commented out to show that `GROUP BY` does not need sorted input. |
| Commented lines in `ZDEMO_ITAB_ANY_LIKE*` | Would raise `ITAB_ILLEGAL_OPERAND` or `OBJECTS_WA_NOT_COMPATIBLE` | Uncomment to see the runtime errors. |
| `ZATI_CL_DEPENDED_ON_COMPONENT~add` | `ASSERTION_FAILED` | Forces tests to isolate the dependency. |

## Assertion dumps in paired demos

`YBW_JOIN` asserts its result equals `YBW_INTERSECT=>EXECUTE_INTERSECT( )`, and `YBW_WINDOWING4` asserts equality with `YBW_WINDOWING_ABAP=>READ_VOTES_AGGREGATED( )`. If one dumps with `ASSERTION_FAILED`, check whether someone changed only one side of the pair, or whether extra rows were added to `YBW_PARTY` by `YBW_TIPPS4`. `YBW_WINDOWING4` compares two `UP TO 10 ROWS` results ordered only by `votes descending`, so ties in vote counts could also make the comparison fail.

## Tools

- **ADT debugger** with hex view, for `YBW_TIPPS6`.
- **ST05 SQL trace**, mentioned in `YBW_TIPPS6` for finding the implicit commit.
- **ADT trace points**, also mentioned in `YBW_TIPPS6`.
- **ST22** for reading runtime error details.

## Related pages

- [Pitfalls](../background/pitfalls.md)
- [Election dataset](../features/election-dataset.md)
- [Testing](testing.md)
