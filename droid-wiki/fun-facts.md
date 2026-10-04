# Fun facts

A few things worth knowing that do not fit elsewhere.

## Sometimes an 'a' is not 'a'

`src/art_data_access/ybw_tipps6.clas.abap` declares:

```abap
asap           type string value `аsap`, "аs soon as possible :)
```

The first letter of `аsap`, and the first letter of the comment, is the Cyrillic small letter a (U+0430, bytes `D0 B0` in UTF-8), not a Latin `a`. It looks identical in an editor. The class header gives a hint: "Use the hex view of the debugger to see the difference. HINT: sometimes an 'a' is not 'a' :)". The same class also hides an implicit commit inside a macro named `wait-for-db`, so a `main` method with four method calls contains two separate bugs.

## The German election as test data

Instead of the usual flight booking tables, the ABAP SQL session uses the results of the 26 Sep 2021 German federal election. `YBW_TIPPS1` checks existence with `voting_date = '20210926'`, `YBW_WINDOWING1` partitions candidates by age with `2021 - birthyear`, and `YBW_TIPPS4` inserts two fictional parties named "Die Bestesten" and "Uebermotiviert2021". The `YBW` prefix most likely stands for "Bundestagswahl".

## A World Cup in an internal table

`src/itab_news/zdemo_itab_group_by_sample.prog.abap` groups 32 national teams by confederation using an ABAP `ENUM` (`afc`, `caf`, `concacaf`, `conmebol`, `ofc`, `uefa`). The type is called `ty_wm_team` (WM for Weltmeisterschaft), and the list matches the 2022 FIFA World Cup in Qatar, which started four months after the demo was committed in Jul 2022. The `ofc` value is declared but no team uses it.

## Two programs, one name

`src/itab_news/zdemo_itab_key_alias.prog.abap` and `src/itab_news/zdemo_itab_key_alias_ext.prog.abap` both begin with `report zdw_lt_key_alias.`, a name that matches neither file. The `zdw_lt` prefix is probably the name of the program in the system where the demo was first written, before it was copied into the `ZDEMO_ITAB_*` naming scheme.

## The Rabax comments

In SAP jargon, ABAP runtime errors (short dumps) are often called "Rabax". The itab demos use the word as a heading for commented-out lines that would crash:

```abap
  " Rabax: ITAB_ILLEGAL_OPERAND (New)
  "----------------------------------
*  delete table lv_string from 1.
```

## A dependency built to fail

`src/test_isolation/zati_cl_depended_on_component.clas.abap` implements `add( )` as a single line: `assert 1 = 0.` The real dependency can never succeed, so any unit test that forgets to replace it with a test double dumps immediately. `subtract( )` works but is never called by anything.

## Ghost folders

Git history contains two folders that existed for less than an hour: src/REST_JSON/ (created and deleted on 9 Jul 2022) and src/ITAB_NEWS/ in upper case (created and deleted within a minute on 28 Jul 2022, just before the lower-case `src/itab_news/` arrived). The only file left from that episode, `src/itab_news/README.md`, still contains a single blank line.

## Zero TODOs

There are no `TODO`, `FIXME`, or `HACK` comments anywhere in `src/`. The demos mark unfinished or invalid code differently: by commenting it out and explaining why, as in the "this is not possible in CDS View" notes in `src/new_gen_cds_views/z_classic_view.ddls.asddls`.

## Related pages

- [Lore](lore.md)
- [Debugging](how-to-contribute/debugging.md)
