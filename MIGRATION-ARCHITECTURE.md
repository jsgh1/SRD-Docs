# SRD repository migration — 2026-10-22

The following repositories contain an isolated snapshot of SRD source as of 2026-10-22.
This is a genuine extraction of existing work. It does **not** imply that historical HU branches, PRs, or QA approvals previously existed.

- SRD-Backend: 510 original files
- SRD-Frontend: 169 original files
- SRD-Database: 74 original files
- SRD-Docker-Infra: 296 original files
- SRD-Docs: 108 original files

## Source and build assembly

- All 1157 files from the source snapshot have a recorded destination and SHA-256 checksum in `migration-control/SOURCE_MAPPING.csv`.
- Laravel database migrations are located only in `SRD-Database/services/<service>/database/migrations/`. They are overlaid into the temporary build workspace using the assembly script.
- Never run the application directly from an isolated code repository without assembling its dependencies.
- Build artifacts, documentation, and user secrets must not be included in source code repositories.
- The current VPS deployment remains unchanged while the split is tested.

## Promotion and QA

1. Submit migration/feature PRs into `dev`, with tests.
2. Assemble exact ref/SHA versions of Backend, Frontend, Database and Docker Infra, then run Docker integration and end-to-end tests on a separate test environment.
3. Promote **the tested commit set** through PRs to `qa`, and only then through PRs to `main`.
4. Do not claim production readiness until release tests and functional acceptance are recorded.

## Authorship

The package contains `publish-as-jsgh1.ps1`, which authenticates with the repository owner's own `gh` CLI session, configures Git commit author information as `jsgh1`, and pushes changes from the owner's computer. No connector application commit authors are used by this method.

## English policy

Code identifiers, test names, branch names, commits, PR titles, comments, and technical error messages should be written in English. End-user Spanish/English translations and academic documents must remain in their intended natural languages. The translation audit is **not complete**; renaming database columns or public API fields requires backward-compatible migrations.
