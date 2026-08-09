# Release operation

Each versioned repository keeps one thin local release job. The job targets the
`release` environment, whose custom deployment branch policy allows only `main`,
and pins `.github/actions/release` from this repository to a full commit SHA.

The shared composite action:

1. mints a short-lived `ternforge-release` token for exactly the caller repository and verifies that scope;
2. creates or updates one Release Please PR and, for Python callers, synchronizes only `uv.lock` on that PR branch;
3. after a release is created, mints a second token from the same Release App limited to exactly `ternforge-infra-updates`, verifies that scope, and sends one `repository_dispatch` wake-up.

The caller supplies the public App client ID from its repository variable and the
private key from its protected `release` environment secret. Python callers also
enable lock synchronization. No repository-level release key, `secrets: inherit`,
or reusable-workflow secret plumbing is used. Existing `v*` tags are protected
by immutable no-bypass tag rulesets. The Release Please PR is the human release
boundary; updater and caller changes remain ordinary reviewed PRs.
