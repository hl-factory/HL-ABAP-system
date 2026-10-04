# Testing

The only automated tests are the ABAP Unit tests in `src/test_isolation/`, and they are themselves the subject of a demo. Everything else is verified by running it and reading the output, helped by a few `assert` statements that compare two demos.

## ABAP Unit tests

All tests live in one test include: `src/test_isolation/zati_cl_code_under_test.clas.testclasses.abap` (510 lines). It belongs to `ZATI_CL_CODE_UNDER_TEST`.

| Test class | Test methods | Framework |
| --- | --- | --- |
| `ltc_call_other_object` | `double_without_framework` | None (hand-written `ltd_depended_on_component`) |
| `ltc_call_other_object_fw` | `double_with_framework` | `cl_abap_testdouble` |
| `ltc_call_function_module` | `fm_answer_1_expect_1`, `fm_answer_2_expect_2`, `fm_answer_a_expect_a` | `cl_function_test_environment` |
| `ltc_select_database_table` | `aggregation`, `empty_table` | `cl_osql_test_environment` |
| `ltc_select_cds_entity` | `aggregation`, `empty_table` | `cl_cds_test_environment` |
| `ltc_call_authority_check` | `display_authorization`, `edit_authorization` | `cl_aunit_authority_check` |
| `ltcl_call_rap_bo_tx_bf_dbl` | `isolate_create_ba_to_pass` | `cl_botd_txbufdbl_bo_test_env` |
| `ltcl_call_rap_bo_mock_eml_api` | `isolate_create_ba_to_pass` | `cl_botd_mockemlapi_bo_test_env` |

That is 8 test classes and 13 test methods. All are `duration short risk level harmless`.

### Running them

In ADT, open `src/test_isolation/zati_cl_code_under_test.clas.abap` and run ABAP Unit (Ctrl+Shift+F10), or right-click the package and choose *Run As > ABAP Unit Test*. There is no CI job that runs them.

### Test structure

Each test method follows Given / When / Then with comments:

```abap
  method fm_answer_1_expect_1.
    " Given
    final(test_double) = g_function_test_environment->get_double( 'POPUP_TO_CONFIRM' ).
    ...
    " When
    final(result) = m_cut->call_function_module( ).

    " Then
    cl_abap_unit_assert=>assert_equals( act = result
                                        exp = 1 ).
  endmethod.
```

Lifecycle per test class:

- `class_setup`: create the framework environment once (for example `cl_osql_test_environment=>create( value #( ( 'DEMO_SALES_SO_I' ) ) )`).
- `setup`: `clear_doubles( )` and get a fresh CUT from `zati_cl_factory=>get_code_under_test( )`.
- `class_teardown`: `destroy( )` the environment, for the SQL, CDS, and RAP test classes.

### Writing a new test

1. If the dependency is a class, inject a double through `ZATI_TH_INJECTOR` (`src/test_isolation/zati_th_injector.clas.abap`). Never instantiate the CUT or DOC with `NEW` in a test; they are `create private`.
2. For tables, CDS entities, function modules, authority checks, or RAP BOs, use the matching framework environment as the existing classes do.
3. Keep tests `risk level harmless`: the doubles mean nothing touches real data.

The real `ZATI_CL_DEPENDED_ON_COMPONENT~add` is `assert 1 = 0`, so a test that forgets to inject a double fails at once. Note that the injector's `clear( )` method is never called by the tests, so an injected DOC double stays in the factory's static attribute for the rest of the test run. See [Pitfalls](../background/pitfalls.md).

## Verifying demos without tests

| Package | How to check a change |
| --- | --- |
| `src/art_data_access/` | Load data ([Election dataset](../features/election-dataset.md)), run the class with F9, compare the console output with what the comments describe. `YBW_JOIN` and `YBW_WINDOWING4` assert equality with `YBW_INTERSECT` and `YBW_WINDOWING_ABAP`, so if you change one of the pair, run both. |
| `src/itab_news/` | Run with F8. `ZDEMO_ITAB_ANY_LIKE` and `ZDEMO_ITAB_ANY_LIKE_EXT` assert that the generic and static versions give the same table. |
| `src/new_gen_cds_views/` | Activate. The point of the demos is what the syntax check accepts, so activation is the test. Then use data preview. |

## Coverage

Only `src/test_isolation/` is covered: 510 lines of test code for 201 lines of production code and CDS. The other three packages (about 2,400 lines of ABAP and CDS) have no tests, which is normal for demo code.

## Related pages

- [Test isolation](../packages/test-isolation.md)
- [Debugging](debugging.md)
