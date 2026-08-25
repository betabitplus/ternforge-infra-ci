# ternforge-infra-ci

Shared CI and release building blocks for Ternforge repositories.

## Shared automation

- `.github/workflows/python-library.yml` runs the direct locked Python-library
  quality, security, test, offline documentation, audit, build, metadata,
  manifest, and isolated artifact checks behind the stable `ci / required`
  interface. Repositories with `features/**/*.feature` replay only their
  canonical `tests/*/bdd` living-specification bindings with VCR in `none` mode
  and network blocking; that dedicated replay ignores unrelated project-wide
  pytest `addopts`, applies Allure's built-in zero-failure quality gate, and
  retains the raw Allure evidence for 30 days. Repositories with `docs/conf.py`
  build Sphinx-Gallery pages with live example execution disabled; repositories
  not yet migrated to Sphinx skip that step.
- `.github/actions/release/action.yml` runs Release Please with a short-lived
  repository-scoped GitHub App token and optionally synchronizes a Python
  `uv.lock` in the Release PR branch.
- `.github/workflows/python-library-docs.yml` publishes exact release tags to
  GitHub Pages only when docs, examples, the supported public Python surface,
  living-specification inputs, or the effective docs/specifications dependency
  environment changed since the last published site. When features exist, the
  same Pages artifact also contains an Allure 3 Awesome report at
  `/specifications/`, with native `history.jsonl` trends and a zero-failure
  quality gate. `manual` mode reports stale docs on a release and waits for an
  explicit live run; `release` mode builds automatically.

Consumers pin shared automation to a full 40-character commit SHA and retain a
human-readable release-tag comment next to the pin. Repository lifecycle and
release operation are documented in [`docs/release.md`](docs/release.md).

The optional private-repository personal-Mac fallback is documented in
[`local-ci/README.md`](local-ci/README.md). GitHub-hosted execution remains the
default when `TERNFORGE_RUNNER` is absent. Environment-bound live docs can use
`bin/ternforge-live-docs publish OWNER/REPO`, which creates a one-job ephemeral
macOS runner and leaves Pages packaging/deployment on GitHub-hosted Actions. The
helper pins a Node-24-compatible Actions Runner so current artifact actions do not
depend on whatever runner version happens to be installed on the host.
