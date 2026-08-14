# Local CI fallback

GitHub-hosted Actions remains the default. When private-repository included
minutes are exhausted, one personal Apple Silicon Mac can temporarily run the
same Ternforge jobs through the local pool.

```bash
./bin/ternforge-local-ci enable
./bin/ternforge-local-ci status
./bin/ternforge-local-ci reconcile
./bin/ternforge-local-ci disable
```

`enable` creates/starts one pinned Lima VZ ARM64 Ubuntu VM, reconciles the
current managed fleet, then installs a per-user macOS `launchd` job that reruns
the same idempotent reconciliation every 60 seconds. `disable` removes the
periodic job first, restores hosted routing, unregisters repository runners,
and only then stops the VM.

A repository is eligible only when both the committed
`ternforge-infra-repository-control/fleet.auto.tfvars.json` visibility and live
GitHub readback are `private`. Eligible repositories get exactly one
repository-scoped runner with label `ternforge-local` and the transient
repository variable `TERNFORGE_RUNNER=ternforge-local`. Public or mismatched
repositories have neither. The fleet inventory remains the only desired-state
repository list; live GitHub state is readback only.

The VM uses guest-local storage with no host filesystem mount, Docker socket,
Rosetta, or SSH-agent forwarding. Runner auto-update is disabled; Lima, GitHub
Actions Runner, and GitHub CLI versions live in `versions.env` and are updated
by Renovate. The exact Ubuntu image release and digest are intentionally updated
together only with a reviewed local-CI acceptance run because Ubuntu cloud
images do not have a native Renovate datasource in the current setup.
