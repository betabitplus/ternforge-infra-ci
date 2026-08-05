# Release operation

`ternforge-infra-ci` owns two reusable workflow interfaces:

- `.github/workflows/python-library.yml` publishes the stable `ci / required`
  check through its permanent `required` job;
- `.github/workflows/release.yml` creates or updates one Release Please pull
  request and, for Python callers, synchronizes only `uv.lock` in the documented
  `pr.headBranchName` branch.

## Credential boundary

The permanent `ternforge-release` GitHub App is installed on selected
repositories only. Its repository permissions are limited to:

```text
contents: write
pull requests: write
metadata: read (mandatory)
```

The caller passes the App Client ID through the
`TERNFORGE_RELEASE_CLIENT_ID` repository variable and one explicitly named
`TERNFORGE_RELEASE_PRIVATE_KEY` repository secret. `secrets: inherit` is not
used. Every invocation mints a short-lived token restricted to the current
repository and verifies that the installation token sees exactly that one
repository before Release Please runs.

GitHub does not allow a caller job that uses a reusable workflow to target an
environment. Consequently an environment secret cannot be passed into
`workflow_call`; the private key is stored as one named repository secret while
the manual merge of the Release PR remains the release decision.

## First-release bootstrap

The first release temporarily uses `.github/workflows/release-bootstrap.yml`,
which calls the reusable workflow from the same commit. After `v0.1.0` exists,
the bootstrap caller is deleted and replaced by a permanent caller pinned to the
full commit SHA behind the released tag with a human-readable tag comment.

The task is not complete until a second release succeeds through the permanent
exact-SHA caller and no bootstrap workflow, branch, credential, or tag exception
remains.

## Release behavior

Feature and fix pull requests may use GitHub auto-merge after `ci / required`.
The Release Please pull request is always merged manually. Existing `v*` tags
are protected by a no-bypass immutable tag ruleset. Post-release update dispatch
is deliberately absent until `ternforge-infra-updates` is implemented in Task
0006.
