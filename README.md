# OpenRPC Spec-Types

Generated OpenRPC specification types for TypeScript, Go, Python, and Rust. This package is a replacement for `meta-schema`.
It not only creates types in a per language way, but also exposes the corresponding raw schemas, so you can use a single
package to do validation. It includes all of the versioned schemas from the spec repository.

## Packages

| Language   | Install                                                      | Registry |
| ---------- | ------------------------------------------------------------ | -------- |
| TypeScript | `npm install @open-rpc/spec-types`                           | npm      |
| Go         | `go get github.com/zcstarr/spec-types/generated/packages/go` | git tags |
| Python     | --                                                           | WIP      |
| Rust       | --                                                           | WIP      |

Each package exposes versioned submodules (e.g. `v1_3`, `v1_4`) matching the OpenRPC spec versions.

## Repo Structure

```
src/                              # Generator (Bun/TS) - reads JSON schemas, emits typed packages
generated/packages/{ts,go,py,rs}  # Output packages (committed to repo)
knope.toml                        # Multi-package release config
test-builds.sh                    # Local smoke tests
.github/workflows/                # CI pipelines
```

## Development

**Prerequisites:** [Bun](https://bun.sh)

```sh
bun install            # install deps
bun run generate       # regenerate all packages from schemas
bun run lint           # eslint
```

Generated output is committed. Run `bun run generate` and commit the result when schemas or generator code change.

## CI

Four workflows in `.github/workflows/`:

- **check-generated** -- On PRs: regenerates types and diffs against committed output. Fails if stale.
- **test-installs** -- On pushes/PRs: smoke-tests TS and Go builds. Py/Rs available via manual `workflow_dispatch`.
- **prepare-release** -- On push to `main`: knope auto-creates a `release` branch + PR with version bumps and changelogs.
- **release** -- On merge of the release PR: creates GitHub releases, publishes packages (TS to npm, Go via tags; Py/Rs disabled for now).

## Tip: Knope Commit Scoping

Knope uses conventional commit **scopes** to route changes to the right package.

Scopes: `ts`, `go`, `py`, `rs`

```
feat(ts): add new field to ContentDescriptor
fix(go): correct json tag on MethodObject
chore(py): update pyproject metadata
```

An unscoped commit like `feat: something` will apply to **all** packages -- every package gets the changelog entry and version bump. Use scopes to target specific packages.

## Tip: Local Test Builds

`test-builds.sh` runs pack/install/build smoke tests in isolated temp directories:

```sh
./test-builds.sh
```

By default it tests TS and Go. Py and Rs are commented out (WIP) -- uncomment them in the script as needed.
