# Background

This section explains why the repository looks the way it does. Most of these decisions follow from its purpose: it is teaching material for live sessions, not production code. For the chronological story, see [Lore](../lore.md).

## Design decisions

### One package per talk, no shared code

Each session's demos live in their own package with their own data and dependencies (see [Packages](../packages/index.md)). Attendees can import the whole repository and go straight to the talk they watched, and a presenter can change a demo without affecting another talk. The cost is some duplication: `src/test_isolation/` and `src/new_gen_cds_views/` both rely on SAP demo tables, and `src/art_data_access/` defines its own tables.

### Real data instead of SAP demo tables

The ABAP SQL session uses German election data rather than the SAP flight model. The likely reason is that the flight tables are too small and uniform to make window functions, set operations, and performance comparisons interesting, while election results have hundreds of areas, many parties, and thousands of candidates. SAP cannot redistribute the data, so it ships a loader (`src/art_data_access/ybw_load_data.clas.abap`) and makes users fetch the files themselves under the German open-data licence. See [Election dataset](../features/election-dataset.md).

### Demos that compare old and new

Most demos show a before and after side by side:

- `Z_CLASSIC_VIEW` versus `Z_DEMO_NO_1` in `src/new_gen_cds_views/`, with unsupported lines commented out in the old one.
- `YBW_WINDOWING_ABAP` versus `YBW_WINDOWING4`, linked by an `assert`.
- `AT END OF` versus `GROUP BY` in `src/itab_news/zdemo_itab_group_by.prog.abap` (program title "Demo: AT Versus. GROUP BY", text "Alt gegen Neu").
- Hand-written test double versus framework double in `src/test_isolation/`.

### Intentional bugs and empty results

Some demos are puzzles: `YBW_TIPPS6` hides two bugs, `YBW_CTE` returns nothing and asks why, and `ZDEMO_ITAB_SCND_OPT` is an optimization exercise with the solution left out. These are not defects. See [Pitfalls](pitfalls.md) and [Debugging](../how-to-contribute/debugging.md) before changing them.

### Prefix folder logic

`.abapgit.xml` uses `PREFIX` folder logic, so package names are derived from folder names. That keeps the folder tree and package tree in sync but forces the 11-character limit on the root package name that `README.md` mentions.

### Customer namespace prefixes

All objects use the customer namespace (`Y*` or `Z*`) so they can be imported into any system without a reserved namespace. The art of data access package uses `Y`, while the others use `Z`. The test isolation package moved from a personal prefix (`ZMS`) to a generic one (`ZATI`) in Aug 2023 so that the object names do not identify one person.

## Pages in this section

- [Pitfalls](pitfalls.md): things that commonly go wrong or look wrong
