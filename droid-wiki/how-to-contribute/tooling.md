# Tooling

The repository has no build system, package manager, linter configuration, or CI workflow. The tools that matter are abapGit (serialization), ADT (editing and running), and the REUSE tool (license compliance).

## abapGit

abapGit converts ABAP repository objects to files and back. Its configuration is `.abapgit.xml`:

| Setting | Value | Effect |
| --- | --- | --- |
| `MASTER_LANGUAGE` | `E` | Texts are maintained in English |
| `STARTING_FOLDER` | `/src/` | Only `src/` is deserialized |
| `FOLDER_LOGIC` | `PREFIX` | Folder `x` becomes subpackage `<root>_X` |
| `IGNORE` | `/.gitignore`, `/LICENSE`, `/README.md`, `/package.json`, `/.travis.yml`, `/.gitlab-ci.yml`, `/abaplint.json`, `/azure-pipelines.yml`, `/.devcontainer.json` | Not treated as ABAP objects |

The ignore list looks like abapGit's standard list. None of the CI files it names (`.travis.yml`, `abaplint.json`, and so on) exist in this repository.

Every object XML file starts with a header naming the serializer, for example `<abapGit version="v1.0.0" serializer="LCL_OBJECT_CLAS" serializer_version="v1.0.0">`. Do not hand-edit these unless you know the format; let abapGit regenerate them. The `.ddls.baseinfo` files are JSON that abapGit writes for CDS sources, listing `FROM` and `ASSOCIATED` dependencies.

Two ways to run abapGit, both mentioned in `README.md`:

- **On-premise:** the standalone abapGit report.
- **SAP BTP ABAP environment:** the abapGit plugin for ADT.

abapGit cannot serialize CDS entity buffers (DTEB), which is why one demo object must be created by hand.

## ABAP Development Tools (ADT)

ADT is the Eclipse-based IDE for ABAP (`https://tools.hana.ondemand.com/#abap`). The demos rely on these ADT features:

- **F9 class runner** for classes implementing `IF_OO_ADT_CLASSRUN`.
- **Code completion** (Ctrl+Space) in ABAP SQL field lists, demonstrated by `src/art_data_access/ybw_tipps0.clas.abap`.
- **ABAP Unit runner** for `src/test_isolation/`.
- **Debugger hex view and trace points**, referenced by `src/art_data_access/ybw_tipps6.clas.abap`.
- **New Entity Buffer** wizard, needed for the manual DTEB step.

## REUSE

The repository is set up for the FSFE REUSE specification, and `README.md` shows the REUSE status badge for the upstream repository.

- `REUSE.toml` (added Mar 2025) declares the package name, supplier contact, download location, an SAP disclaimer about API calls to external products, and one annotation: `path = "**"` with `SPDX-License-Identifier = "Apache-2.0"` and copyright "2022 SAP SE or an SAP affiliate company and abap-platform-fundamentals-01 contributors".
- `LICENSES/Apache-2.0.txt` holds the license text referenced by the SPDX identifier.
- Before Mar 2025 the same information lived in .reuse/dep5 (Debian copyright format). Commit `d4a3180` "Reuse Version update from dep to toml" replaced it.

To check compliance locally, install the `reuse` Python tool and run `reuse lint` in the repository root. Because the annotation covers `**`, new files are covered automatically.

## What is absent

- No `abaplint.json` or other static analysis config.
- No GitHub Actions or other CI.
- No `.gitignore`.
- No test runner outside the SAP system.

## Related pages

- [Configuration](../reference/configuration.md)
- [Development workflow](development-workflow.md)
