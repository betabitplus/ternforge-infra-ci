# Release operation

Each versioned repository keeps a thin caller that pins the reusable
`.github/workflows/release.yml` workflow to a full commit SHA.

The reusable workflow:

1. mints a short-lived `ternforge-release` token for exactly the caller repository and verifies that scope;
2. creates or updates one Release Please PR and, for Python callers, synchronizes only `uv.lock` on that PR branch;
3. after a release is created, mints a second token from the same Release App limited to exactly `ternforge-infra-updates`, verifies that scope, and sends one `repository_dispatch` wake-up.

Callers pass only the explicitly named Release App private-key secret; Python
callers additionally enable lock synchronization. The public App client ID comes
from the repository variable read by the reusable workflow; `secrets: inherit` is
not used. Existing `v*` tags are protected by immutable no-bypass tag rulesets.
The Release Please PR is the human release boundary; updater and caller changes
remain ordinary reviewed PRs.
