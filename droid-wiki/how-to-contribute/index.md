# How to contribute

This repository is a copy of an archived SAP sample repository, so contributing works differently from an application codebase. There is no issue tracker workflow, CI, or release process in the repo itself. Changes are edits to ABAP objects that you make in an SAP system and serialize back to Git with abapGit.

## Where work comes from

- **Upstream:** `README.md` says to open issues at `https://github.com/SAP-samples/abap-platform-fundamentals-01/issues` and ask questions in SAP Community. The upstream repository now sits under `SAP-archive`, which usually means it is read-only, so new upstream contributions are unlikely to be accepted.
- **This copy (`hl-factory/HL-ABAP-system`):** no contribution process is defined. Treat changes as ordinary pull requests against `main`.

## Pull request process

Upstream history shows the pattern: contributors worked on a branch or fork (`DSAG_TheArtOfDataAccess`, `ajinkyapatil8190-patch-1`, `Reuse-Migration-TOML-Branch`, forks such as `sautermi0/main`) and opened a pull request that a maintainer merged with a merge commit. Seven of the 48 commits on `main` are merges. Many other commits were made directly on `main` through the GitHub web UI ("Add files via upload", "Update README.md").

`README.md` states that upstream contributors had to accept the Linux Foundation DCO on their first pull request.

## Review expectations

There are no automated checks. A reviewer should confirm:

- The changed objects activate without syntax errors in a target system.
- abapGit-generated XML (`.clas.xml`, `.ddls.xml`, `.tabl.xml`, `package.devc.xml`) was committed along with the source and is not empty. The empty `src/new_gen_cds_views/package.devc.xml` is an example of what slips through without this check.
- `README.md` lists any new demo object under the right session.
- Licensing metadata in `REUSE.toml` still covers the new files (the `path = "**"` rule does this automatically).

## Definition of done

- Objects activate and run with the documented shortcut (F9, F8, or ABAP Unit).
- Demos that assert equality with another demo still pass.
- Unit tests in `src/test_isolation/` pass if that package changed.
- `README.md` is updated.

## Pages in this section

- [Development workflow](development-workflow.md): edit in SAP, serialize with abapGit, commit
- [Testing](testing.md): the ABAP Unit tests and how to verify demos
- [Debugging](debugging.md): common import and runtime errors
- [Patterns and conventions](patterns-and-conventions.md): naming and coding style per package
- [Tooling](tooling.md): abapGit, ADT, and REUSE
