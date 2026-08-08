# Release operation

Each versioned repository keeps a thin caller that pins the reusable
`.github/workflows/release.yml` workflow to a full commit SHA.

The reusable workflow:

1. mints a short-lived `ternforge-release` token for exactly the caller repository and verifies that scope;
2. creates or updates one Release Please PR and, for Python callers, synchronizes only `uv.lock` on that PR branch;
3. after the Release PR is merged and a release is created, mints a short-lived `ternforge-dispatch` token for exactly `ternforge-infra-updates`, verifies that scope, and sends one `repository_dispatch` wake-up.

Callers pass public App client IDs through repository variables and explicitly
named private-key secrets; `secrets: inherit` is not used. Existing `v*` tags are
protected by immutable no-bypass tag rulesets. The Release Please PR is the human
release boundary; updater and caller changes remain ordinary reviewed PRs.
