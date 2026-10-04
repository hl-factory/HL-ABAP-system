# Dependencies

The repository has no package manager and no third-party libraries. Its dependencies are SAP-delivered repository objects that must already exist in the target system, plus one external website. This page lists every one found in the source, grouped by package.

## Counts

| Category | Count |
| --- | --- |
| SAP database tables read | 6 (`SFLIGHT`, `SPFLI`, `SCARR`, `SAIRPORT`, `DEMO_RENT`, `DEMO_SALES_SO_I`) |
| SAP data elements | 1 (`S_CARR_ID`) |
| SAP RAP business objects | 1 (`/DMO/I_TRAVEL_M`) |
| SAP function modules | 1 (`POPUP_TO_CONFIRM`) |
| SAP authorization objects | 1 (`S_DEVELOP`) |
| SAP test frameworks | 7 environment or helper classes, plus `CL_ABAP_UNIT_ASSERT` |
| Other SAP classes and interfaces | 9 |
| External services | 1 (`www.bundeswahlleiter.de`) |

## SAP tables, views, and BOs

| Object | Used by | Purpose |
| --- | --- | --- |
| `SFLIGHT`, `SPFLI` | `Z_CLASSIC_VIEW`, `Z_DEMO_NO_1` (and their extensions) | Flight model: flights and connections |
| `SCARR` | `Z_CLASSIC_VIEW`, `Z_DEMO_NO_1` association `_scarr` | Flight model: carriers |
| `SAIRPORT` | `Z_DEMO_ENTITY_BUFFER` | Flight model: airports |
| `S_CARR_ID` | Parameter `p_carrid` of both flight views | Data element for carrier ID |
| `DEMO_RENT` | `Z_DEMO_CALCULATED_QUANTITY` | ABAP demo table with apartment rents |
| `DEMO_SALES_SO_I` | `ZATI_CDS_ENTITY`, `ZATI_CL_CODE_UNDER_TEST~SELECT_DATABASE_TABLE`, test doubles | ABAP demo table with sales order items |
| `/DMO/I_TRAVEL_M` | `ZATI_CL_CODE_UNDER_TEST~CALL_RAP_BUSINESS_OBJECT`, RAP test classes | RAP managed travel BO from the ABAP Flight Reference Scenario; also needs `/DMO/TRAVEL_ID` |

## SAP function modules and authorization objects

| Object | Used by |
| --- | --- |
| `POPUP_TO_CONFIRM` | `ZATI_CL_CODE_UNDER_TEST~CALL_FUNCTION_MODULE`; doubled by `CL_FUNCTION_TEST_ENVIRONMENT` |
| `S_DEVELOP` (field `ACTVT`) | `ZATI_CL_CODE_UNDER_TEST~CALL_AUTHORITY_CHECK`; restricted by `CL_AUNIT_AUTHORITY_CHECK` |

## SAP test frameworks (`src/test_isolation/`)

| Class or interface | Framework | First release (from `README.md`) |
| --- | --- | --- |
| `CL_ABAP_TESTDOUBLE` | ABAP OO Test Double Framework | SAP BASIS 740 SP9 |
| `CL_FUNCTION_TEST_ENVIRONMENT`, `IF_FUNCTION_TEST_ENVIRONMENT` | Function Module Test Double Framework | SAP NetWeaver 756 |
| `CL_OSQL_TEST_ENVIRONMENT`, `IF_OSQL_TEST_ENVIRONMENT` | ABAP SQL Test Double Framework | SAP NetWeaver 752 |
| `CL_CDS_TEST_ENVIRONMENT`, `IF_CDS_TEST_ENVIRONMENT` | ABAP CDS Test Double Framework | SAP NetWeaver 751 |
| `CL_AUNIT_AUTHORITY_CHECK`, `CL_AUNIT_AUTH_CHECK_TYPES_DEF` | Classic ABAP Authority Check Test Helper API | not stated |
| `CL_BOTD_TXBUFDBL_BO_TEST_ENV`, `IF_BOTD_TXBUFDBL_BO_TEST_ENV`, `IF_BOTD_BUFDBL_FIELDS_HANDLER` | RAP transactional buffer double | not stated |
| `CL_BOTD_MOCKEMLAPI_BO_TEST_ENV`, `IF_BOTD_MOCKEMLAPI_BO_TEST_ENV`, `CL_BOTD_MOCKEMLAPI_BLDRFACTORY` | RAP Mock EML API | not stated |
| `CL_ABAP_UNIT_ASSERT` | ABAP Unit | long-standing |

## Other SAP classes and interfaces

| Object | Used by | Notes |
| --- | --- | --- |
| `IF_OO_ADT_CLASSRUN` | All runnable `YBW_*` classes | ADT F9 class runner |
| `CL_DEMO_OUTPUT` | `YBW_TIPPS6`, most `ZDEMO_ITAB_*` programs | Output window |
| `CL_HTTP_CLIENT` | `YBW_LOAD_DATA` | HTTP download; classic API |
| `CL_ABAP_ZIP` | `YBW_LOAD_DATA` | Extract candidate CSV from ZIP |
| `CL_ABAP_CONV_IN_CE` | `YBW_LOAD_DATA` | Convert XSTRING to string |
| `CL_ABAP_CHAR_UTILITIES` | `YBW_LOAD_DATA` | Newline constant for CSV splitting |
| `CL_ABAP_RANDOM_INT` | `ZDEMO_ITAB_SCND_OPT` | Seeded random data |
| `CL_ABAP_TABLEDESCR` | `ZDEMO_ITAB_KEY_ALIAS_EXT` | RTTI for key aliases |
| `IF_ABAP_BEHV` | `ZATI_CL_CODE_UNDER_TEST`, `ltd_fields_handler` | RAP constants (`mk-on`, `op-m-create`) |

## External services

| Service | Used by | Notes |
| --- | --- | --- |
| `https://www.bundeswahlleiter.de` | `YBW_LOAD_DATA` | Open data for the 2021 federal election, under "Data licence Germany, attribution, version 2.0". Two of three URLs must be set manually. See [Election dataset](../features/election-dataset.md). |

## Freshness

There are no versioned dependencies to update. The practical "freshness" risks are:

- The Bundeswahlleiter URLs, which the loader itself describes as unstable.
- The RAP test double APIs and `FINAL( )` syntax in `src/test_isolation/`, which need a recent ABAP release; older systems cannot activate that package.
- DDIC-based CDS views and classic HTTP APIs, which SAP is moving away from in ABAP Cloud.

## Related pages

- [Architecture](../overview/architecture.md)
- [Getting started](../overview/getting-started.md)
