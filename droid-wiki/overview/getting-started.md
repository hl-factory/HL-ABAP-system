# Getting started

This page covers how to get the demo objects into an SAP system and run them. There is no local build or test command: the code only compiles and runs inside an ABAP system.

## Prerequisites

- **An SAP ABAP system you can develop in.** `README.md` mentions two options: an on-premise system (import with the abapGit report) or SAP BTP ABAP environment, called "Steampunk" in the README (import with the abapGit plugin for ADT). Some packages only work on one of these; see "Which system for which package" below.
- **ABAP Development Tools (ADT)**, the Eclipse-based IDE, installed from `https://tools.hana.ondemand.com/#abap` as described in `README.md`.
- **abapGit**, either the standalone report on-premise or the ADT plugin.
- For general workshop system requirements, `README.md` points to the RAP workshop requirements document in `SAP-samples/abap-platform-rap-workshops`.

## Install

1. Create a root package in your system, for example `TEST_DSAG01` or `Z_DSAG01`. Keep the name to 11 characters or fewer. abapGit uses prefix folder logic (`<FOLDER_LOGIC>PREFIX</FOLDER_LOGIC>` in `.abapgit.xml`), so subpackage names are built from the root name plus the folder name, and long root names push them past the package name length limit.
2. Link the repository URL to that package in abapGit and pull. abapGit reads `.abapgit.xml`, starts at `/src/`, and creates one subpackage per folder.
3. Activate the imported objects.
4. **New generation CDS views only:** create the CDS entity buffer manually, because abapGit does not support object type DTEB. Right-click `z_demo_entity_buffer` in ADT, choose *New Entity Buffer*, and paste the source from `README.md`:

   ```text
   define view entity buffer on z_demo_entity_buffer
          layer core
          type generic number of key elements 1
   ```

## Run your first demo

The quickest check that the import worked is a class from `src/art_data_access/` that does not need data, or a report from `src/itab_news/` that builds its own data:

- Open `src/itab_news/zdemo_itab_group_by.prog.abap` (program `ZDEMO_ITAB_GROUP_BY`) and press F8. It fills an internal table with nine rows and prints group sums twice, once with `AT END OF` and once with `LOOP AT ... GROUP BY`.
- Open `src/itab_news/zdemo_itab_key_alias.prog.abap` and press F8 to see access to a sorted table through primary and secondary key aliases.

## Load data for the SQL demos

The `YBW_*` tables in `src/art_data_access/` ship empty. Run class `YBW_LOAD_DATA` (`src/art_data_access/ybw_load_data.clas.abap`) with F9 to fill them from the German Federal Returning Officer's open data. Two of its three URL constants are set to the placeholder `` `Set URL` ``; until you replace them, the class only prints a licence hint. The full procedure is on [Election dataset](../features/election-dataset.md).

After loading, run the session-1 classes in the order `README.md` lists them (`YBW_JOIN`, `YBW_UNION`, `YBW_INTERSECT`, `YBW_EXCEPT`, `YBW_CTE`, `YBW_TIPPS0` to `YBW_TIPPS3`), then the session-2 classes.

## Run the unit tests

Open `ZATI_CL_CODE_UNDER_TEST` (`src/test_isolation/zati_cl_code_under_test.clas.abap`) and run its ABAP Unit tests (Ctrl+Shift+F10 in ADT). The test classes in `src/test_isolation/zati_cl_code_under_test.clas.testclasses.abap` are the only automated tests in the repository. See [Testing](../how-to-contribute/testing.md).

## Which system for which package

These notes are inferred from the code; the repository does not state them explicitly.

| Package | Needs |
| --- | --- |
| `src/art_data_access/` | Any system with ADT class runner support. `YBW_LOAD_DATA` uses `CL_HTTP_CLIENT`, `CL_ABAP_ZIP`, and `CL_ABAP_CONV_IN_CE`, which are classic APIs and probably not available in ABAP Cloud. `YBW_TIPPS6` calls `CL_DEMO_OUTPUT=>DISPLAY`. |
| `src/new_gen_cds_views/` | The SAP flight model (`SFLIGHT`, `SPFLI`, `SCARR`, `SAIRPORT`) and `DEMO_RENT`. `Z_CLASSIC_VIEW` is a DDIC-based view (`@AbapCatalog.sqlViewName`), which ABAP Cloud does not allow. |
| `src/test_isolation/` | `DEMO_SALES_SO_I`, the RAP reference business object `/DMO/I_TRAVEL_M`, function module `POPUP_TO_CONFIRM`, and a release recent enough for `FINAL( )` declarations and the RAP test double APIs. |
| `src/itab_news/` | Executable programs with selection screens (`REPORT`, `PARAMETERS`), so a system that allows classic reports. |

## Next steps

- [Running demos](../features/running-demos.md) for the three execution styles
- [Debugging](../how-to-contribute/debugging.md) for common errors after import
- [Packages](../packages/index.md) for what each demo shows
