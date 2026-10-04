# HL-ABAP-system overview

HL-ABAP-system is a copy of SAP's `abap-platform-fundamentals-01` sample repository: ABAP, ABAP CDS, and ABAP SQL code snippets that SAP presented in educational online sessions (DSAG talks and global user group webinars). It contains no application. Each abapGit package under `src/` holds the demo objects for one talk, and you import them into an SAP system to run them.

## What is in the repository

The repository has four ABAP packages. The top-level `README.md` documents three of them; the fourth (`src/itab_news/`) was added later and never got a README section.

| Package | Session | Object prefix | Page |
| --- | --- | --- | --- |
| `src/art_data_access/` | "ABAP SQL: the art of accessing data" | `YBW_*`, `YDEMOS4_*` | [Art of data access](../packages/art-data-access.md) |
| `src/new_gen_cds_views/` | "A new generation of CDS views: CDS view entities" | `Z_*` | [New generation CDS views](../packages/new-gen-cds-views.md) |
| `src/test_isolation/` | "Why aren't my tests stable? Test isolation with the ABAP Unit framework" | `ZATI_*` | [Test isolation](../packages/test-isolation.md) |
| `src/itab_news/` | DSAG internal table demos ("DSAG Itab Demos" in `src/itab_news/package.devc.xml`) | `ZDEMO_ITAB_*`, `ZCL_DEMO_ITAB_*` | [Itab news](../packages/itab-news.md) |

The `README.md` also lists two RAP sessions ("Fundamentals" and "Entity Manipulation Language") as planned. Neither was ever added.

## Who uses it

- ABAP developers who attended or watched one of the sessions and want to re-run the demos in their own system.
- Trainers who need small, self-contained examples of modern ABAP SQL, CDS view entities, internal table syntax, or ABAP Unit test doubles.

There is no build, no CI, and no runtime outside an SAP ABAP system. The "product" is the source code you read and execute in ABAP Development Tools (ADT).

## Upstream and this copy

The content comes from `https://github.com/SAP-archive/abap-platform-fundamentals-01` (originally published under `SAP-samples`). This copy lives at `https://github.com/hl-factory/HL-ABAP-system`. The only change on top of upstream is a merge of an "Initial commit" stub created when the hl-factory repository was set up in Oct 2026. The merge kept the upstream `README.md` unchanged. See [Lore](../lore.md) for the full history.

## Quick links

- [Architecture](architecture.md): how the packages, DDIC objects, and SAP standard dependencies fit together
- [Getting started](getting-started.md): importing the code with abapGit and running the first demo
- [Glossary](glossary.md): ABAP and SAP terms used throughout the wiki
- [Running demos](../features/running-demos.md): how each kind of demo object is executed
- [Election dataset](../features/election-dataset.md): the German federal election 2021 data behind the SQL demos
- [Data models](../reference/data-models.md): every table, table type, and CDS entity in the repo

## Licensing

The code is Apache-2.0 licensed (`LICENSE`, `LICENSES/Apache-2.0.txt`). Licensing metadata for the REUSE tool lives in `REUSE.toml`, which replaced the older .reuse/dep5 file in Mar 2025.
