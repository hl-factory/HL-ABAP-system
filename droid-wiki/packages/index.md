# Packages

Each folder under `src/` is an ABAP package created by abapGit, and each package holds the demos for one educational session. The packages are independent: none references objects in another. The root package `src/package.devc.xml` has the description "Sample programs for ABAP SQL".

| Package | Session | Files | Main object types | Added |
| --- | --- | --- | --- | --- |
| [Art of data access](art-data-access.md) | ABAP SQL: the art of accessing data (parts 1 and 2) | 71 | 26 classes, 7 tables, 2 data elements, 2 CDS view entities | Mar to Apr 2022 |
| [New generation CDS views](new-gen-cds-views.md) | A new generation of CDS views: CDS view entities | 19 | 6 CDS data definitions | Jul 2022, reworked Jan and May 2023 |
| [Test isolation](test-isolation.md) | Why aren't my tests stable? Test isolation with the ABAP Unit framework | 17 | 4 classes, 2 interfaces, 1 CDS view entity | Apr 2023, renamed Aug 2023 |
| [Itab news](itab-news.md) | DSAG internal table demos | 27 | 9 programs, 2 classes, 1 structure, 2 table types | Jul 2022 |

The order above follows `README.md`. Itab news is listed last because `README.md` does not mention it.

## Choosing where to start

- To learn modern ABAP SQL, start with [Art of data access](art-data-access.md), but load the data first ([Election dataset](../features/election-dataset.md)).
- To compare the two CDS view types, read [New generation CDS views](new-gen-cds-views.md). No data loading is needed if the flight demo tables are filled.
- To learn ABAP Unit test doubles, read [Test isolation](test-isolation.md) and run its tests.
- For internal table syntax, open any program in [Itab news](itab-news.md); they generate their own data.

## Related pages

- [Architecture](../overview/architecture.md)
- [Running demos](../features/running-demos.md)
