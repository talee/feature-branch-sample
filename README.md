# feature-branch-sample

Created to help friends understand some feature branching tips.

## Start
- Dev creates feature branch
- QA create automation branch

## Development
QA needs new changes from feature branch on automation branch

```
git rebase --onto feature_branch{,} automation_branch
```
Command does the following
- backups current branch (automation branch) commits that are **not** in feature_branch
- overwrites the current branch with the feature_branch (hard reset, changes the base)
- then copies the automation changes commits to the current branch

Current branch **base** is now feature_branch with automation commits on top

If feature_branch is updated, simply run the same command to pull in the new changes.

## Create automation PR
QA needs to apply only their automation changes to develop branch (no dev commits included in PR)
```
git checkout -b automation_PR_branch
git rebase --onto develop feature_branch automation_PR_branch
```
- clones current branch to automation_PR_branch
- backups current branch (automation_PR_branch) commits that are **not** in feature_branch
- overwrites the current branch with develop (hard reset, changes the base)
- then copies the automation changes commits to the current branch

## Update automation PR

Cherry picks all new changes in automation branch and applies them to automation_PR branch. This will overwrite everything in the current PR branch.
```
git checkout automation_PR_branch
git reset --hard automation_branch
git rebase --onto develop feature_branch automation_PR_branch
```
Using rebase over cherry pick since it covers all new changes without having to specify a range

## Visual confirmation on GitHub.com

Replace the following in the URL:
- ORG_OR_USER with organization or name
- REPOSITORY with name of Git repository
- develop with the branch you're requesting a PR to
- automation_PR_branch with the branch contain PR commits

https://github.com/ORG_OR_USER/REPOSITORY/compare/develop...automation_PR_branch

Actual example link: https://github.com/talee/feature-branch-sample/compare/autoA-PR

## Undoing mistakes

You should always check via `git log` after rebasing to ensure your commits have actually been applied correctly. If you notice things out of place, `git reflog` will show you your git command history and allow you to reset back to before your rebase.

If I needed to go back to HEAD@{3} based on reflog:
```
git reset --hard HEAD@{3}
```
