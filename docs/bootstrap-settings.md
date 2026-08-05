# Task 0001 bootstrap record

This record preserves the bootstrap state that will later be adopted by
`ternforge-infra-repository-control`. It contains no credentials or secret
values.

## Inputs

- Ternforge planning commit: `betabitplus/betabit-notes@de0936592b499c161eec211af2dd62485a06c093`
- Frozen production baseline: `betabitplus/py-lib-starter@d59582375855cff69fb165e467dc5847bc75ca99`
- Initial repository: `betabitplus/ternforge-infra-ci`
- Permanent branch: `main`

## Bootstrap boundary

The account-owner credential is used only for the unavoidable manual creation
of this first personal-account repository and its bootstrap commit. Subsequent
Task 0001 mutations use a temporary GitHub App installed only on
`betabitplus/ternforge-infra-ci`. The installation repository set is read back
before use, and access to `betabitplus/py-lib-starter` must be denied.

The temporary App and its private key are revoked after Task 0001 acceptance.
No personal access token, installation token, private key, or credential value
is committed, logged, or uploaded as workflow evidence.

## Required repository state

```text
default branch = main
permanent branches = main only
allow auto-merge = true
delete merged branches = true
allow squash merge = true
allow merge commits = false
allow rebase merge = false
squash title = PR_TITLE
squash message = PR_BODY
default Actions permissions = read
Actions may approve pull requests = false
```

## Main ruleset

```text
include = refs/heads/main
pull request required
allowed merge method = squash
required status check = ci / required
required expected source = GitHub Actions App
strict required status checks = true
required approving reviews = 0
deletion prohibited
non-fast-forward prohibited
bypass actors = none
```

The required workflow has no path filters and handles `opened`, `reopened`,
`synchronize`, and `edited` pull request events.

## Acceptance and rollback

Acceptance uses one real short-lived branch and pull request. An invalid title
must fail, editing the same pull request to a Conventional Commit title must
produce a new successful check, and GitHub auto-merge must create a squash
commit using the pull request title and body before deleting the branch.

Before dependent work exists, rollback is deletion of this repository after
revoking the temporary App. After acceptance, repository changes use normal
pull requests and controlled ruleset changes; no direct hotfix path is created.
