# Configuration

The repository has very little configuration: one abapGit file, one REUSE file, the package definitions, and a few constants inside the data loader. There are no environment variables or runtime settings.

## `.abapgit.xml`

Read by abapGit when the repository is linked to a package.

```xml
<DATA>
 <MASTER_LANGUAGE>E</MASTER_LANGUAGE>
 <STARTING_FOLDER>/src/</STARTING_FOLDER>
 <FOLDER_LOGIC>PREFIX</FOLDER_LOGIC>
 <IGNORE>
  <item>/.gitignore</item>
  <item>/LICENSE</item>
  <item>/README.md</item>
  <item>/package.json</item>
  <item>/.travis.yml</item>
  <item>/.gitlab-ci.yml</item>
  <item>/abaplint.json</item>
  <item>/azure-pipelines.yml</item>
  <item>/.devcontainer.json</item>
 </IGNORE>
</DATA>
```

| Key | Effect |
| --- | --- |
| `MASTER_LANGUAGE` | Original language of texts (English) |
| `STARTING_FOLDER` | Only `/src/` is mapped to ABAP objects |
| `FOLDER_LOGIC` | `PREFIX`: subfolder `x` maps to package `<ROOT>_X`; subpackage names must start with the root name |
| `IGNORE` | Files abapGit never treats as objects |

Changing `FOLDER_LOGIC` to `FULL` would let packages have arbitrary names but would require renaming every folder to the target package name. Leave it as is unless you know you need that.

## Package definitions

| File | Description (`CTEXT`) |
| --- | --- |
| `src/package.devc.xml` | Sample programs for ABAP SQL |
| `src/art_data_access/package.devc.xml` | The Art of data access |
| `src/itab_news/package.devc.xml` | DSAG Itab Demos |
| `src/new_gen_cds_views/package.devc.xml` | (empty file) |
| `src/test_isolation/package.devc.xml` | Test Isolation Demo |

The root package description dates from the first commit of the art of data access package and was never updated when other topics were added.

## `REUSE.toml`

License metadata for the REUSE tool, added in Mar 2025 to replace .reuse/dep5.

| Key | Value |
| --- | --- |
| `version` | `1` |
| `SPDX-PackageName` | `abap-platform-fundamentals-01` |
| `SPDX-PackageSupplier` | `safa.golrokh.bahoosh@sap.com` |
| `SPDX-PackageDownloadLocation` | `https://github.com/sap-samples/abap-platform-fundamentals-01` |
| `SPDX-PackageComment` | SAP's standard disclaimer about API calls to SAP or third-party "External Products" |
| `[[annotations]]` `path` | `**` |
| `precedence` | `aggregate` |
| `SPDX-FileCopyrightText` | 2022 SAP SE or an SAP affiliate company and abap-platform-fundamentals-01 contributors |
| `SPDX-License-Identifier` | `Apache-2.0` (text in `LICENSES/Apache-2.0.txt`) |

## Data loader constants

`src/art_data_access/ybw_load_data.clas.abap` declares three public constants that act as configuration. They must be edited in the source before the loader does anything.

| Constant | Shipped value | Expected content |
| --- | --- | --- |
| `c_url_votes_csv` | `https://www.bundeswahlleiter.de/bundestagswahlen/2021/ergebnisse/opendata/daten/kerg2_00287.csv` | Election results CSV (`kerg2_*.csv`) |
| `c_url_parties_csv` | `Set URL` | `btw21_parteien.csv` |
| `c_url_candidates_zip` | `Set URL` | `btw21_gewaehlte_utf8.zip`, containing `btw21_gewaehlte-fortschreibung_utf8.csv` |

`if_oo_adt_classrun~main` checks only the two placeholder constants; if either equals `` `Set URL` ``, it prints the licence hint and stops. The ZIP entry name `btw21_gewaehlte-fortschreibung_utf8.csv` is hard-coded in `load_csw_via_url`.

## Demo parameters

A few demos have values you might tweak:

| Object | Setting | Default |
| --- | --- | --- |
| `ZDEMO_ITAB_SCND_OPT` | `co_item_cnt`, `co_order_cnt` constants; `rep_cnt` parameter; `cust_id` | 200,000; 300,000; 1; 1234 |
| `ZDEMO_ITAB_STEP` | `linno`, `step`, `von`, `bis` selection parameters | 20, 1, 1, 20 |
| `YBW_TIPPS1` | `DO 100 TIMES` loop count | 100 |
| `Z_DEMO_NO_1`, `Z_CLASSIC_VIEW` | CDS parameters `p_carrid`, `p_abap_int4`, `p_abap_dec` | Supplied at query time |

## Related pages

- [Tooling](../how-to-contribute/tooling.md)
- [Election dataset](../features/election-dataset.md)
