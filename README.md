# ternforge-infra-ci

Shared CI and release building blocks for Ternforge repositories.

## Reusable workflows

- `.github/workflows/python-library.yml` runs the direct locked Python-library
  quality, security, test, audit, build, metadata, manifest, and isolated
  artifact checks behind the stable `ci / required` interface.
- `.github/workflows/release.yml` runs Release Please with a short-lived
  repository-scoped GitHub App token and optionally synchronizes a Python
  `uv.lock` in the Release PR branch.

Consumers pin reusable workflows to a full 40-character commit SHA and retain a
human-readable release-tag comment next to the pin. Repository lifecycle and
release operation are documented in [`docs/release.md`](docs/release.md).
