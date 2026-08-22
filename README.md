# ternforge-infra-ci

Shared CI and release building blocks for Ternforge repositories.

## Shared automation

- `.github/workflows/python-library.yml` runs the direct locked Python-library
  quality, security, test, offline documentation, audit, build, metadata,
  manifest, and isolated artifact checks behind the stable `ci / required`
  interface. Repositories with `docs/conf.py` build Sphinx-Gallery pages with
  live example execution disabled; repositories not yet migrated to Sphinx skip
  that step.
- `.github/actions/release/action.yml` runs Release Please with a short-lived
  repository-scoped GitHub App token and optionally synchronizes a Python
  `uv.lock` in the Release PR branch.

Consumers pin shared automation to a full 40-character commit SHA and retain a
human-readable release-tag comment next to the pin. Repository lifecycle and
release operation are documented in [`docs/release.md`](docs/release.md).

The optional private-repository personal-Mac fallback is documented in
[`local-ci/README.md`](local-ci/README.md). GitHub-hosted execution remains the
default when `TERNFORGE_RUNNER` is absent.
