# Licence allowlist and checks

## Default allowlist

For anything that ships or runs in production: MIT, Apache-2.0, BSD-2-Clause, BSD-3-Clause, ISC, 0BSD, Unlicense, PostgreSQL, MPL-2.0.

Often allowed for development-only dependencies, with the owner's agreement: BlueOak-1.0.0, Python-2.0, CC0-1.0, CC-BY-4.0, MIT-0.

Not allowed without written approval: GPL, LGPL, AGPL, SSPL, BUSL, Elastic License, Commons Clause, source-available or unknown licences.

Record exceptions as decision records, and add a project standing rule pointing to each one.

## Checks by ecosystem

Pick what the project already uses; otherwise add a board task for platform-engineer to wire one into CI as a required check.

| Ecosystem | Check |
| --- | --- |
| pnpm | `pnpm licenses list --json --prod`, compared with the allowlist by a small script |
| npm / yarn | `npx license-checker-rseidelsohn --production --onlyAllow "<list joined by ;>"` |
| Python | `pip-licenses --allow-only="<list joined by ;>"` |
| Go | `go-licenses check ./... --allowed_licenses=<list joined by ,>` |
| Rust | `cargo deny check licenses`, with the allowlist in `deny.toml` |
| JVM | the Gradle or Maven license plugin with an allowlist |
| .NET | `dotnet-project-licenses` with an allowed list |
| Containers | list base-image OS packages in the bill of materials; review, do not gate, unless the owner asks |
