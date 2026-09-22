# AGENTS.md - how to work in a repo generated from this template

This is a Rust project scaffolded from [grenudi/rustnix](https://github.com/grenudi/rustnix):
a Nix + direnv devenv (isolated VSCodium, pinned toolchain - see `README.md`) plus CI, release
automation, and a test convention, all wired up from the first commit.

## Setup (once per session)
```
./init_devenv.sh   # trusts .envrc and loads the Nix devshell; see README.md
cargo test --workspace --all-features
```
If `Cargo.toml` still says `name = "rust-boilerplate"`, this repo hasn't been renamed for its
actual project yet - see the README's "Renaming for your actual project" section before writing
real code.

## Conventions
1. **Conventional Commits on the PR title**, not necessarily every commit inside it - `pr-title.yml`
   lints the title, and `release-plz` reads the squash-merged result to decide the version bump and
   changelog entry. See `CONTRIBUTING.md` for the exact type/scope format.
2. **One test file per concern** under `tests/`, not one large `tests.rs` - see
   [argenv](https://github.com/argenv-opencommons/argenv)'s `crates/argenv/tests/*.rs` for the
   pattern this follows.
3. **`cargo fmt` and `cargo clippy --all-targets -- -D warnings` must both be clean** before a PR -
   CI enforces this, but check locally first; `--all-targets` means a lint firing only in a test or
   example still counts.
4. **`cargo doc` must build with no warnings** (`RUSTDOCFLAGS=-D warnings`) - undocumented public
   items are a CI failure, not a style nit.
5. **MSRV is 1.75.0** (`Cargo.toml`'s `rust-version`, `clippy.toml`'s `msrv`, and the CI `msrv` job
   all need to move together if this ever changes).
6. **Never edit `CHANGELOG.md` by hand** - `release-plz` generates it from commit history via
   `cliff.toml`. A commit can add a `User-Facing: <text>` footer to control exactly what shows up
   in the changelog line, when the raw commit message isn't the right wording for a release note.

## Releases
Every push to `main` updates one open release PR (version bump + changelog). Merging it cuts the
release and, once `CARGO_REGISTRY_TOKEN` is set as a repo secret, publishes to crates.io. Nothing
else triggers a release - no manual tagging, no separate publish step.

## Why it's built this way
This template exists so a new project's *process* (CI, releases, test layout) doesn't need to be
re-derived by hand each time, the same way `rustnix`'s Nix devenv already saves you from re-deriving
the IDE setup. The specific choices (release-plz over release-please, PR-title linting over
per-commit linting, `tests/` layout) mirror what's already proven out in
[argenv](https://github.com/argenv-opencommons/argenv), not invented fresh here.
