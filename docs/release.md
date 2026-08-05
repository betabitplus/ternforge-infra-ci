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
used. The reusable release job binds itself to the caller repository's `release`
environment, which permits protected branches only and has no separate reviewer
gate.

The manual merge of the Release Please pull request is the sole human release
decision. Each invocation mints a short-lived token restricted to the current
repository and verifies that the installation token sees exactly that one
repository before Release Please runs.

## Released self caller

The first release used a temporary same-commit bootstrap caller. That caller was
removed immediately after `v1.0.0`. The permanent
`.github/workflows/release-caller.yml` pins the released reusable
workflow to the full commit SHA behind `v1.3.0` with a human-readable tag
comment.

A release caller update is always a normal reviewed pull request. Existing
callers never follow a moving branch or tag, and no bootstrap workflow, branch,
credential, or tag exception remains.

## Release behavior

Feature and fix pull requests may use GitHub auto-merge after `ci / required`.
The Release Please pull request is always merged manually. Existing `v*` tags
are protected by a no-bypass immutable tag ruleset. Post-release update dispatch
is deliberately absent until `ternforge-infra-updates` is implemented in Task
0006.
