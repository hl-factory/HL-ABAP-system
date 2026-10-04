# Patterns and conventions

The four packages were written by different people for different talks, so conventions vary by package. This page lists what each package does consistently, so new or changed demos can match their neighbors.

## One package per session

Every talk gets its own folder under `src/` with a `package.devc.xml`. Packages do not reference each other. A new session should get a new folder, not new objects in an existing one. Remember that the folder name becomes part of the subpackage name under prefix folder logic (see [Configuration](../reference/configuration.md)).

## Naming

| Package | Prefix | Pattern |
| --- | --- | --- |
| `src/art_data_access/` | `YBW_` (election tables and demos), `YDEMOS4_` (char-to-numc tables) | Topic plus number: `YBW_WINDOWING0`..`4`, `YBW_TIPPS0`..`6`, `YBW_CHAR_TO_NUMC_1`..`6` |
| `src/new_gen_cds_views/` | `Z_` | Descriptive: `Z_CLASSIC_VIEW`, `Z_DEMO_NO_1`, `Z_VIEW_EXTENSION` |
| `src/test_isolation/` | `ZATI_` | Role-based infixes: `ZATI_CL_` (class), `ZATI_IF_` (interface), `ZATI_TH_` (test helper) |
| `src/itab_news/` | `ZDEMO_ITAB_`, `ZCL_DEMO_ITAB_` | Feature name: `ZDEMO_ITAB_STEP`, plus `_EXT` or `_SYNTAX` variants |

Inside the test classes, ABAP Unit naming conventions apply: `ltc_` for local test classes, `ltcl_` for the two RAP test classes, and `ltd_` for local test doubles.

The `ZATI_` prefix replaced a personal `ZMS` prefix in Aug 2023 (commit "Change prefix from personal to generic"). Keep new objects on generic prefixes.

## Demo class shape (art_data_access)

Nearly every runnable class in `src/art_data_access/` follows the same template (`YBW_LOAD_DATA` skips the ABAP Doc header). `src/art_data_access/ybw_union.clas.abap` is a typical example:

```abap
"! <p class="shorttext synchronized" lang="en">Demonstrates set operation UNION</p>
"!
"! ...what the demo shows...
"!
"! Please execute the class in the eclipse editor aka. ADT with shortcut F9.
"! The result will be displayed in the console
class ybw_union definition
  public
  final
  create public .

  public section.
    interfaces: if_oo_adt_classrun.
  ...
endclass.

class ybw_union implementation.
  method if_oo_adt_classrun~main.
    " explanation of the query
    select ...
    into table @data(result).
    out->write( result ).
  endmethod.
endclass.
```

Conventions within this template:

- An ABAP Doc header (`"!`) with a `shorttext synchronized` description, a paragraph explaining the concept, and the F9 instruction.
- Lower-case keywords throughout (the exception is `src/art_data_access/ybw_candidate_manager.clas.abap`, which uses upper case).
- Strict ABAP SQL syntax: `select from ... fields ...`, `@` host variables, inline `@data(...)` targets.
- Inline `"` comments that explain the query and sometimes give the reader a task ("Play with the statement to see why that is").
- Multiple result sets separated by `out->write( '----...' )` lines and labelled with `out->write( 'name:' )`.
- When two demos must produce the same result, the second one checks it with `assert`, for example `assert join_result = ybw_intersect=>execute_intersect( ).` in `src/art_data_access/ybw_join.clas.abap` and the comparison against `ybw_windowing_abap=>read_votes_aggregated( )` in `src/art_data_access/ybw_windowing4.clas.abap`.

## Report shape (itab_news)

`src/itab_news/` uses executable programs instead of classes. They:

- start with `report <name>.` and declare local types at the top,
- build their own test data with `value #( ... )` constructor expressions,
- print with `cl_demo_output=>new( )` and `out->write( )`, then `out->display( )`,
- often come in pairs: a short version and an `_ext` version (`zdemo_itab_any_like` / `zdemo_itab_any_like_ext`, `zdemo_itab_key_alias` / `zdemo_itab_key_alias_ext`) or a runnable version and a `_syntax` cheat sheet (`zdemo_itab_step` / `zdemo_itab_step_syntax`).

Lines that would raise a runtime error are kept but commented out, with the error name above them:

```abap
  " Rabax: ITAB_ILLEGAL_OPERAND (New)
  "----------------------------------
*  delete table lv_string from 1.
```

## Test isolation pattern (test_isolation)

`src/test_isolation/` follows a factory-plus-injector pattern for dependency injection:

- Production classes are `create private` and list the factory as a `global friend`, so only `ZATI_CL_FACTORY` can instantiate them.
- `ZATI_CL_FACTORY` returns a cached double if one is set, otherwise a new real instance (`cond #( when ... is not bound then new ... else ... )`).
- `ZATI_TH_INJECTOR` is `for testing` and a global friend of the factory, so only test code can set the doubles.
- Each test method uses `" Given`, `" When`, `" Then` comments.
- `FINAL( )` inline declarations instead of `DATA( )`.

Details are on [Test isolation](../packages/test-isolation.md).

## CDS source conventions (new_gen_cds_views)

The CDS files are teaching material, so they keep invalid or unsupported lines as comments with a note explaining why. In `src/new_gen_cds_views/z_classic_view.ddls.asddls`, lines such as `// and 1 = 1 -- Expression: this is not possible in CDS View` show what the old view type rejects, while `src/new_gen_cds_views/z_demo_no_1.ddls.asddls` contains the same lines uncommented with notes like `-- literal on left side of ON condition`. Both `//` and `--` comment styles appear.

## Error handling

There is little error handling, by design: these are demos. Where it exists:

- `src/art_data_access/ybw_load_data.clas.abap` uses `assert fields rmsg condition sy-subrc = 0` after each HTTP call, so a failure produces a short dump that includes the message text.
- Classic exceptions from `cl_http_client` are mapped to `sy-subrc` values and checked.
- Demos use `assert` to prove two queries match, so a mismatch also dumps.

## Writing style in comments

Comments are explanatory and sometimes playful (`"аs soon as possible :)"`, `HINT: sometimes an 'a' is not 'a' :)` in `src/art_data_access/ybw_tipps6.clas.abap`). Some identifiers and output labels are German (`partei_name`, `keine_partei`, `Stimmen`, `Gruppe`) because the data is German.

## Related pages

- [Development workflow](development-workflow.md)
- [Tooling](tooling.md)
- [Glossary](../overview/glossary.md)
