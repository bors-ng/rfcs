When a PR depends upon another one that's been merged, update the unmerged PR base branch to point to the merged PRs base branch.

> I allow this RFC document to be modified and redistributed under the terms of the [Apache-2.0 license](http://www.apache.org/licenses/LICENSE-2.0), or the [CC-BY-NC-SA 3.0 license](http://creativecommons.org/licenses/by-nc-sa/3.0/deed.en_US), at your option.

# Motivation

The current behaviour causes dependant PRs to be closed then the merged branch is deleted. Github doesn't allow editing the PR or re opening it: a new PR needs to be created, losing any discussion that was already in the original.

# Guide-level explanation

Supponse we have 2 PRs A (base branch `default`) and B (base branch `a`) and A is merged by bors. If bors.toml contains `update_base_for_deletes = true`, then bors will update B to have base `default` before deleting branch `a`. 

# Reference-level explanation

An optional setting `update_base_for_deletes` (default `false`) will be added to bors.toml to enable/disable the feature.

When the branch deleter determines it should delete the branch for a merged/closed PR `closed_pr`, it will first query the Github PR API for any pull request that have `base` set to `closed_pr.head_ref`. It will then update the `base` to point to `closed_pr.base` using the Github API again.

If `use_squash_merge` is disabled, then the updated PR will show a message with the base branch change, but nothing else will change since github ignores commits that already exist on `default`. Otherwise, if squash merges are enabled, the closed PR commits will be listed but the changes ignored in the UI and by git for the next squash merge.

# Drawbacks

* Adds a new configuration option, increasing complexity.
* Github UI will be confusing if `use_squash_merge` is enabled

# Rationale and alternatives

*Why is this design the best in the space of possible designs?*
This is not a one-size-fits-all solution, since workflows can exist that would be made harder by the proposed behaviour. Hiding the feature behind a flag allows users to opt in when they fell they'd reap the benefits.

*What other designs have been considered and what is the rationale for not choosing them?*
Stop bors from deleting branches if PRs exist with it as `base`. While this reduces the manual work required by developers by avoiding the need to create a new PR, it also introduces a potential foot gun. Those PRs can inadvertently be merged onto a branch that is already considered merged, causing devs to think their changes have been integrated on the default branch when they weren't. While you could argue that updating the base branch is also a potential foot gun since you can merge onto the default branch without realizing, I can't think of a workflow where I'd want to merge changes onto an already integrated branch.
 
*What is the impact of not doing this?*
Manual work is required whenever a PR with others depending on it is required, and review context is lost each time that ocurrs. In our case, this caused team members to stop using bors altogether due to the friction to their workflows.

# Prior art

If you use Github to merge PRs, it does this automatically by default.

# Unresolved questions

* What parts of the design do you expect to resolve through the RFC process before this gets accepted?
 * Final choice of configuration option name since the current one doesn't carry the full meaning

* Whether it would be desired to implement the behaviour where bors doesn't delete a branch if PRs point to it can be implemented regardless of this feature.

# Future possibilities

Github is previewing an API to rebase a PR that could be used after updating the `base` of a PR, potentially cleaning up the commit history of the PR.

# See also

* [PR #1023](https://github.com/bors-ng/bors-ng/pull/1023) implements the proposal
* This solution was previously suggested in a topic titled ["Better handling PRs with dependencies"](https://forum.bors.tech/t/better-handling-prs-with-dependencies/409)
* The issue was raised in [#950](https://github.com/bors-ng/bors-ng/issues/950) in the repo

# Implementation status

**Implemented**:

* Pull request: https://github.com/bors-ng/bors-ng/pull/1023
