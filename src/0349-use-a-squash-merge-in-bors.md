Summary: Implement a configuration option for bors to produce "squash merge" commits. When this option is enabled, instead of attaching all commits under the approved pull requests to a batch merge commit, bors will generate one, single, commit for each approved pull request.

> I allow this RFC document to be modified and redistributed under the terms of the [Apache-2.0 license](http://www.apache.org/licenses/LICENSE-2.0), or the [CC-BY-NC-SA 3.0 license](http://creativecommons.org/licenses/by-nc-sa/3.0/deed.en_US), at your option.

# Motivation

Currently, Bors merges in the entire commit history of a PR.  This ends up polluting the git history with huge numbers of small commits that don't matter.  Github solves this by offering a "Squash and merge" functionality.  This will compress the entire commit history into a single commit and provide a good commit title with a link back to the PR.

Squash and merge functionality is widely used in the Github community and is a blocker for rolling out Bors to more locations.

There are several open bugs/feature requests that want the functionality of a squash merge in Bors.  Eg: https://forum.bors.tech/t/is-anyone-working-on-squash-merge-for-bors/340 and https://github.com/bors-ng/bors-ng/issues/194

# Guide-level explanation

To have Bors use the Github "squash-and-merge" functionality.

Edit your bors.toml file and add `use_squash_merge = true`. (The default value of `use_squash_merge` is `false`.)

When you run `bors r+`, bors will operate as normal, except when your batch passes:

1. All changes in each PR will be squashed together, so there is one commit per PR.
2. The commit message will be set to use the PR title with a link to the PR, and the the commit details will contain the commit messages that have been squashed together.

# Reference-level explanation

The basic functional loop of this feature is:

1. Create the staging branch
2. If the `use_squash_merge` flag is set to true for the report then
1. For each Patch 
  * Get the Git tree of the final commit in each patch
  * Create a commit on the staging branch with the commit tree (this is a squash commit)
  * Generate a commit title from the PR title
  * Generate a commit text body from the PR description
  * Create a merge commit (if needed) of all squashed commits 
3. Run the regular Bors build checks
1. When a batch passes all required checks - For each Patch in a Batch
 * Change the title of the patch from "$title" to "[Merged By Bors] - $title"
  

# Drawbacks

This is an opt-in feature.  I think most people would get a better experience if it was just enabled by default.  

# Rationale and alternatives

* Why is this design the best in the space of possible designs?

This change leaves all other aspects of the code-base alone.  The functionality is exclusively opt-in, and does not require any additional services, process, or resources to be installed in the Bors container.  The code changes are non-obtrusive 

* What other designs have been considered and what is the rationale for not choosing them?

One alternative is using the green button merge functionality.  This works but could allow some code to be merged without being fully validated against the tests.

* What is the impact of not doing this?

This is a highly demanded feature and not implementing it will hamper adoption of Bors in the wider world.

# Prior art

See https://forum.bors.tech/t/is-anyone-working-on-squash-merge-for-bors/340 and https://github.com/bors-ng/bors-ng/issues/194 for prior discussions of this.

# Future possibilities

The alternative option could become easy to support if Github adds a more robust Git manipulation API.

# Implementation status

**Implemented**:

* Pull request: https://github.com/bors-ng/bors-ng/pull/718

