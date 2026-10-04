# Architecture

The repository is a set of independent ABAP demo packages serialized by abapGit. Nothing here runs on its own: abapGit deserializes the files into repository objects in an SAP system, and you run each demo from ABAP Development Tools (ADT). This page explains how the files map to ABAP objects, how the packages relate to each other, and which SAP-delivered objects they depend on.

## From files to ABAP objects

`.abapgit.xml` at the repository root controls deserialization:

- `STARTING_FOLDER` is `/src/`, so only files under `src/` become ABAP objects.
- `FOLDER_LOGIC` is `PREFIX`. Each subfolder becomes a subpackage whose name is the root package name plus the folder name. If you import into `Z_DSAG01`, `src/test_isolation/` becomes `Z_DSAG01_TEST_ISOLATION`. This is why `README.md` limits the root package name to 11 characters.
- `MASTER_LANGUAGE` is `E` (English).
- `README.md`, `LICENSE`, and a list of CI/tooling files are ignored.

Each ABAP object is stored as one or more files named `<object>.<type>.<part>`:

| Suffix | ABAP object type | Example |
| --- | --- | --- |
| `.clas.abap` + `.clas.xml` | Global class (source + metadata) | `src/art_data_access/ybw_join.clas.abap` |
| `.clas.locals_def.abap`, `.clas.locals_imp.abap`, `.clas.macros.abap` | Class-local types, local classes, macros | `src/art_data_access/ybw_candidate_manager.clas.locals_imp.abap` |
| `.clas.testclasses.abap` | ABAP Unit test classes of a global class | `src/test_isolation/zati_cl_code_under_test.clas.testclasses.abap` |
| `.intf.abap` + `.intf.xml` | Global interface | `src/test_isolation/zati_if_code_under_test.intf.abap` |
| `.prog.abap` + `.prog.xml` | Executable program (report) with text elements | `src/itab_news/zdemo_itab_step.prog.abap` |
| `.ddls.asddls` + `.ddls.xml` + `.ddls.baseinfo` | CDS data definition (source, metadata, dependency info) | `src/new_gen_cds_views/z_demo_no_1.ddls.asddls` |
| `.tabl.xml` | DDIC database table or structure | `src/art_data_access/ybw_vote.tabl.xml` |
| `.ttyp.xml` | DDIC table type | `src/itab_news/zdemo_itab_order_tab_opt.ttyp.xml` |
| `.dtel.xml` | DDIC data element | `src/art_data_access/ybw_d_party.dtel.xml` |
| `package.devc.xml` | Package definition | `src/test_isolation/package.devc.xml` |

## Package layout

```mermaid
graph TD
    Root["Root package (src/package.devc.xml)<br/>'Sample programs for ABAP SQL'"]
    Root --> ADA["art_data_access<br/>26 classes, 7 tables, 2 CDS views"]
    Root --> CDS["new_gen_cds_views<br/>6 CDS data definitions"]
    Root --> TI["test_isolation<br/>4 classes, 2 interfaces, 1 CDS view"]
    Root --> IT["itab_news<br/>9 programs, 2 classes, 1 structure, 2 table types"]
```

The packages do not reference each other. Each one is a separate talk with its own data and its own SAP standard dependencies. Inside a package, demos often reference each other: for example `src/art_data_access/ybw_join.clas.abap` asserts that its result equals `ybw_intersect=>execute_intersect( )`.

## Dependencies on SAP-delivered objects

Most demos read from tables and frameworks that SAP ships with the ABAP platform. They are not in this repository, so the target system must have them.

```mermaid
graph LR
    subgraph repo["This repository"]
        ADA[art_data_access]
        CDS[new_gen_cds_views]
        TI[test_isolation]
        IT[itab_news]
    end
    subgraph sap["SAP-delivered objects"]
        FLIGHT["Flight model<br/>SFLIGHT, SPFLI, SCARR, SAIRPORT"]
        DEMO["ABAP demo tables<br/>DEMO_RENT, DEMO_SALES_SO_I"]
        DMO["RAP reference BO<br/>/DMO/I_TRAVEL_M"]
        TDF["Test double frameworks<br/>CL_ABAP_TESTDOUBLE, CL_OSQL_TEST_ENVIRONMENT, ..."]
        OUT["Output helpers<br/>IF_OO_ADT_CLASSRUN, CL_DEMO_OUTPUT"]
    end
    WEB["bundeswahlleiter.de<br/>CSV open data"]
    CDS --> FLIGHT
    CDS --> DEMO
    TI --> DEMO
    TI --> DMO
    TI --> TDF
    ADA --> OUT
    IT --> OUT
    ADA -->|ybw_load_data via HTTP| WEB
```

See [Dependencies](../reference/dependencies.md) for the full list.

## How a demo runs

The demos use three execution styles. All of them print to a console or output window instead of building a UI.

```mermaid
sequenceDiagram
    participant Dev as Developer in ADT
    participant Obj as Demo object
    participant DB as Database (YBW_* / SAP tables)
    Dev->>Obj: F9 (class with IF_OO_ADT_CLASSRUN)<br/>or F8 (report)<br/>or Ctrl+Shift+F10 (ABAP Unit)
    Obj->>DB: ABAP SQL / CDS read
    DB-->>Obj: result set
    Obj-->>Dev: out->write( ) to ADT console<br/>or CL_DEMO_OUTPUT window<br/>or unit test result
```

Details are on [Running demos](../features/running-demos.md).

## Language breakdown

Counts are lines in tracked files at the current commit.

```mermaid
xychart-beta horizontal
    title "Lines by file type"
    x-axis ["ABAP source", "abapGit XML metadata", "ABAP Unit tests", "CDS DDL", "CDS baseinfo JSON", "Markdown"]
    y-axis "Lines" 0 --> 2500
    bar [2385, 2268, 510, 216, 168, 127]
```

About half of the repository by line count is XML metadata that abapGit generates. The hand-written content is the ABAP and CDS source. See [By the numbers](../by-the-numbers.md) for more.

## Related pages

- [Packages](../packages/index.md) for a page per package
- [Patterns and conventions](../how-to-contribute/patterns-and-conventions.md) for naming and coding style
- [Configuration](../reference/configuration.md) for `.abapgit.xml` and `REUSE.toml`
