# Cleanup opportunities

The repository is small and has no TODO or FIXME comments, no unused dependencies to update, and no large files. What it does have is a handful of leftover artifacts from web uploads, missing documentation, and a few unused objects. Each item below is a small, self-contained fix.

## Broken or empty files

| File | Problem | Suggested fix |
| --- | --- | --- |
| `src/new_gen_cds_views/package.devc.xml` | 0 bytes since the 13 Jan 2023 upload | Restore the package XML from commit `319d533` or re-serialize the package with abapGit |
| `src/itab_news/README.md` | One blank line | Describe the programs, or delete the file and document the package in `README.md` |

## Documentation gaps

- `README.md` does not mention `src/itab_news/` at all. Add a "Package" section listing the nine programs and two classes, as the other packages have.
- `README.md` still lists "ABAP RESTful Application Programming Model - Fundamentals (planned)" and "- Entity Manipulation Language (planned)". Neither exists; remove them or mark them as dropped.
- The REUSE badge and issue links in `README.md` point at `SAP-samples/abap-platform-fundamentals-01`. For this copy they lead to the upstream (now archived) repository.
- The template comment `<!--- Register repository https://api.reuse.software/register, then add REUSE badge: ... -->` in `README.md` is left over from the SAP sample template.
- The install section of `README.md` opens the example package name `TEST_DSAG01` with two backticks and closes it with one, which breaks the inline code formatting.

## Mismatched names

- `src/itab_news/zdemo_itab_key_alias.prog.abap` and `src/itab_news/zdemo_itab_key_alias_ext.prog.abap` both start with `report zdw_lt_key_alias.` The statement should match the program name (`ZDEMO_ITAB_KEY_ALIAS`, `ZDEMO_ITAB_KEY_ALIAS_EXT`).
- `YBW_PARTY`'s `LONG_NAME` field and its secondary index `SHO` both have the description "tests" in `src/art_data_access/ybw_party.tabl.xml`.
- `load_csw_via_url` in `src/art_data_access/ybw_load_data.clas.abap` probably meant `load_csv_via_url`.

## Unused or barely used objects

| Object | Where | Status |
| --- | --- | --- |
| `YBW_INFRA` | `src/art_data_access/ybw_infra.tabl.xml` | 26-field table with no loader; used only as a target row type in `YBW_TIPPS5` |
| `ZATI_IF_DEPENDED_ON_COMPONENT~SUBTRACT` | `src/test_isolation/zati_if_depended_on_component.intf.abap` | Implemented, never called |
| `ZATI_TH_INJECTOR=>CLEAR`, `ZATI_TH_INJECTOR=>INJECT_CODE_UNDER_TEST` | `src/test_isolation/zati_th_injector.clas.abap` | Never called by the tests |
| `opcode` enum | `src/itab_news/zdemo_itab_step.prog.abap`, `src/itab_news/zdemo_itab_step_syntax.prog.abap` | Declared (`itab_operation structure opcode`) but never used |
| `ofc` enum value | `src/itab_news/zdemo_itab_group_by_sample.prog.abap` | Declared, no team assigned |
| `lr` variable and `<lt_any>` field symbol | `src/itab_news/zdemo_itab_key_alias.prog.abap` | Declared but unused in the short version; only the `_ext` version needs them |

## Data inconsistencies

- The `'Partei'` / `'PARTEI'` spelling difference across demos (see [Pitfalls](background/pitfalls.md)). Decide whether the tips demos should use the same value as the loaded data, and say so in a comment either way.
- `c_url_parties_csv` and `c_url_candidates_zip` in `src/art_data_access/ybw_load_data.clas.abap` are placeholders, while `c_url_votes_csv` is hard-coded. Treating all three the same way would make the setup step clearer.

## Code that could be simplified

- `form handle_buttons` in `src/itab_news/zdemo_itab_step.prog.abap` resets each radio button by hand in six branches (about 40 lines). Radio buttons in one `radiobutton group` are already mutually exclusive, so this form is probably unnecessary.

## Related pages

- [Pitfalls](background/pitfalls.md)
- [Lore](lore.md)
