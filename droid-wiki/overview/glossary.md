# Glossary

Terms used in the code, the `README.md`, and this wiki. German words appear because the SQL demos use German election data and some object descriptions are in German.

## ABAP platform and tooling

| Term | Meaning |
| --- | --- |
| **ABAP** | SAP's programming language for business applications. |
| **ADT** | ABAP Development Tools, the Eclipse-based IDE. The demo classes say "execute the class in the eclipse editor aka. ADT with shortcut F9". |
| **abapGit** | Open-source Git client for ABAP. It serializes repository objects to the files in `src/` and back. Configured by `.abapgit.xml`. |
| **Prefix folder logic** | abapGit mode (`FOLDER_LOGIC = PREFIX`) where each folder maps to a subpackage named `<root package>_<FOLDER>`. |
| **Package (DEVC)** | ABAP container for repository objects. Each folder under `src/` has a `package.devc.xml`. |
| **DDIC** | ABAP Dictionary. Holds tables (`.tabl.xml`), data elements (`.dtel.xml`), and table types (`.ttyp.xml`). |
| **Steampunk** | Informal name for SAP BTP ABAP environment (ABAP in the cloud), used in `README.md`. |
| **On prem** | An on-premise SAP system, where abapGit runs as a report. |
| **DSAG** | Deutschsprachige SAP-Anwendergruppe, the German-speaking SAP user group. Several sessions were presented there. |
| **Y/Z namespace** | Customer namespace. Objects starting with `Y` or `Z` belong to customers, not SAP. The SQL demos use `Y`, the other packages use `Z`. |
| **Rabax / short dump** | An ABAP runtime error. `src/itab_news/zdemo_itab_any_like.prog.abap` labels commented-out lines with the runtime error they raise, for example `ITAB_ILLEGAL_OPERAND`. |
| **ST05** | SQL trace transaction. `src/art_data_access/ybw_tipps6.clas.abap` mentions it for analyzing a `DBSQL_INVALID_CURSOR` error. |

## ABAP SQL

| Term | Meaning |
| --- | --- |
| **ABAP SQL** | SAP's database-independent SQL dialect embedded in ABAP (formerly Open SQL). |
| **New / strict syntax** | ABAP SQL with comma-separated field lists and `@`-escaped host variables. Enables stricter checks. Shown in `src/art_data_access/ybw_tipps5.clas.abap`. |
| **Set operation** | `UNION`, `INTERSECT`, `EXCEPT` on two `SELECT` result sets. |
| **CTE** | Common table expression, written `WITH +name AS ( ... )`. Names start with `+`. See `src/art_data_access/ybw_cte.clas.abap`. |
| **Window function** | Aggregate or ranking function with an `OVER( )` clause, such as `row_number( ) over( partition by ... order by ... )`. |
| **Table buffering** | Application-server cache of table contents. `YBW_PARTY` is fully buffered (`PUFFERUNG = X` in `src/art_data_access/ybw_party.tabl.xml`). |
| **Global temporary table (GTT)** | Table whose contents live only until the end of the database transaction. `YDEMOS4_NUMC10_G` has `IS_GTT = X`. |
| **NUMC** | ABAP character type that should hold only digits. The `YBW_CHAR_TO_NUMC_*` classes show how to convert `CHAR` data into it. |

## ABAP CDS

| Term | Meaning |
| --- | --- |
| **CDS** | Core Data Services, SAP's data modeling language on top of SQL. |
| **CDS DDIC-based view** | The older `DEFINE VIEW` with `@AbapCatalog.sqlViewName`, which generates a DDIC SQL view. Example: `src/new_gen_cds_views/z_classic_view.ddls.asddls`. |
| **CDS view entity** | The newer `DEFINE VIEW ENTITY` without a generated DDIC view, with stricter syntax checks and more expression support. |
| **View extension / view entity extension** | `EXTEND VIEW` and `EXTEND VIEW ENTITY`, which add fields or associations to an existing view. |
| **CDS entity buffer (DTEB)** | Repository object that defines table buffering for a view entity. Not supported by abapGit, so it must be created by hand. |
| **Association** | Declared join to another entity, exposed with a leading underscore, such as `_scarr`. |
| **`$parameters`, `$session`** | Access to CDS view parameters and session variables such as `$session.user_date`. |

## ABAP Unit and test isolation

| Term | Meaning |
| --- | --- |
| **CUT** | Code under test. Here `ZATI_CL_CODE_UNDER_TEST`. |
| **DOC** | Depended-on component, anything the CUT calls that a test must replace. Here `ZATI_CL_DEPENDED_ON_COMPONENT` plus function modules, tables, CDS entities, authority checks, and RAP BOs. |
| **Test double** | Replacement for a DOC used during a test. Can be hand-written (`ltd_depended_on_component`) or created by a framework. |
| **Injector / test helper** | Class marked `FOR TESTING` that swaps a double into a factory. Here `ZATI_TH_INJECTOR`. |
| **Global friends** | ABAP mechanism that lets one class access another's private components. The factory and injector pattern relies on it. |
| **RAP** | ABAP RESTful Application Programming Model. |
| **RAP BO** | RAP business object, here SAP's reference `/DMO/I_TRAVEL_M`. |
| **EML** | Entity Manipulation Language, the `MODIFY ENTITIES` statements used to call a RAP BO. |
| **Transactional buffer double** | Framework that replaces a RAP BO's transactional buffer during tests (`CL_BOTD_TXBUFDBL_BO_TEST_ENV`). |
| **Mock EML API** | Framework that replaces EML calls with configured responses (`CL_BOTD_MOCKEMLAPI_BO_TEST_ENV`). |

## Internal tables

| Term | Meaning |
| --- | --- |
| **Internal table (itab)** | In-memory table in ABAP. |
| **Secondary key** | Extra sorted or hashed key on an internal table. `src/itab_news/zdemo_itab_scnd_opt.prog.abap` uses them for performance. |
| **Key alias** | Alternate name for a table key (`... key primary_key alias k1_alias ...`). |
| **`STEP`** | Addition to `LOOP`, `DELETE`, `INSERT`, `APPEND`, `FOR`, and `LINES OF` that processes every n-th line. |
| **`GROUP BY` loop** | `LOOP AT ... GROUP BY` with `LOOP AT GROUP`, the modern replacement for `AT NEW` / `AT END OF`. |

## German election terms

| Term | Meaning |
| --- | --- |
| **Bundestagswahl** | German federal election. The data is from 2021. |
| **Bundeswahlleiter** | Federal Returning Officer. Publishes the open-data CSV files that `YBW_LOAD_DATA` reads. |
| **Partei** | Party. Value of the `KIND` column for real parties. |
| **Einzelbewerber/Wählergruppe** | Individual candidate or voters' group. Filtered in `src/art_data_access/ybw_votes_person.ddls.asddls`. |
| **Erststimme / Zweitstimme** | First vote (constituency candidate) and second vote (party list). Stored in the one-character `YBW_VOTE-VOTE` column; the set operation demos filter `vote = '1'` for first votes. |
| **Gebiet / Bundesgebiet** | Area / the federal territory as a whole. Demos often filter `area <> 'Bundesgebiet'` to drop the national total. |
| **Stimmen** | Votes. |
