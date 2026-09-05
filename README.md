# ternforge-infra-ci

Shared CI and release building blocks for Ternforge repositories.

## Shared automation

- `.github/workflows/python-library.yml` runs the direct locked Python-library
  quality, security, test, offline documentation, audit, build, metadata,
  manifest, and isolated artifact checks behind the stable `ci / required`
  interface. Its single pytest invocation emits JUnit, raw Allure results, and
  coverage.py dynamic contexts. The same job hands the retained JUnit to
  `ternforge-docops build html`, which owns test-evidence ingestion and strict
  engineering-graph validation. Raw test evidence is retained for 30 days.
  Repositories with `docs/conf.py` build through DocOps with live examples
  disabled; repositories without documentation skip that step.
- `.github/actions/release/action.yml` runs Release Please with a short-lived
  repository-scoped GitHub App token and optionally synchronizes a Python
  `uv.lock` in the Release PR branch.
- `.github/workflows/python-library-docs.yml` publishes exact release tags to
  GitHub Pages when documentation, examples, requirements, tests, features,
  implementation source, or the full locked evidence/docs environment changed
  since the last published site. The release build runs pytest once and passes
  the resulting JUnit and Allure evidence to `ternforge-docops build portal`,
  which owns live documentation execution, source/evidence ingestion, graph
  validation, and the published Allure perspectives. Release dossiers are built
  through `ternforge-docops build dossier`. Raw pytest, Allure, and
  coverage-context evidence is retained for 30 days.
  `manual` mode reports stale docs on a release and waits for an explicit live
  run; `release` mode builds automatically.

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
