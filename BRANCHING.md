# Branch and tag policy

## Branches

- `main` is the default branch and is protected.
- Work happens on short-lived branches cut from `main` and named by purpose:
  `feat/...`, `fix/...`, `docs/...`, `chore/...`.
- Changes reach `main` only by pull request, with CI passing.
- No force-push to `main`. Delete a branch once it is merged.

## Releases

- A release is an annotated tag on `main` named `vMAJOR.MINOR.PATCH`
  (for example `git tag -a v1.4.0 -m "v1.4.0"`).
- Tags are not moved or deleted after they are pushed.

## Public and private repositories

- A public repository is updated only from a tagged state of its private
  counterpart, never from an untagged commit or a working branch.
- The commit message of every such update names the private tag it was
  produced from.

## Enforcement

This file describes the policy; it does not enforce it. Branch protection for
`main` (pull request required, status checks required, force-push and deletion
blocked) must be switched on in the GitHub repository settings.
