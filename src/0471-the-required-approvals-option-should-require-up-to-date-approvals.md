The `required_approvals` option should enforce that every piece of code that makes it into the master branch, has been reviewed by the requisite number of people.  However, the current implementation counts approving reviews even if new code has been added to a PR since the review.  This unfortunate situation requires users to manually reason about which code has been adequately reviewed.  Bors should recognize approvals only if they apply to all of the changes which are going to be merged.  For backwards compatibility and flexibility, this should be controlled by a new configuration option.

> I allow this RFC document to be modified and redistributed under the terms of the [Apache-2.0 license](http://www.apache.org/licenses/LICENSE-2.0), or the [CC-BY-NC-SA 3.0 license](http://creativecommons.org/licenses/by-nc-sa/3.0/deed.en_US), at your option.

# Motivation

Suppose you have a project which specifies `required_approvals = 3`:

* Person A creates a PR.
* Reviewers B, C, and D approve it.
* Person A pushes an "extra" commit to the PR.
* Reviewer B runs `bors r+`.

In this situation, bors will happily merge the PR, even though the extra commit wasn't actually approved by anyone except for possibly reviewer B.  This makes the `required_approvals` configuration option much less useful, since it fails to ensure that all changes have been approved by 3 people.

The invariant that `required_approvals = n` means every piece of code that makes it into the master branch has been reviewed by _n_ people is a natural extension of the NRSROSE, and bors should enforce it too.

# Guide-level explanation

The configuration option `up_to_date_approvals = true` causes GitHub Reviews to count towards the number of `required_approvals` only when they are up to date.  Pushing new changes to the PR branch requires prior approving reviews to be resubmitted in order to count.

Enabling `up_to_date_approvals` makes certain that any code merged to master has been adequately reviewed.

# Reference-level explanation

`up_to_date_approvals` is a boolean configuration option in `bors.toml` defaulting to false.

We say review R is *up to date* for commit hash X if R's `commit_id` key as seen through the [GitHub Review API](https://developer.github.com/v3/pulls/reviews/) is exactly X.  Otherwise it is *stale*.  If `up_to_date_approvals` is true, then whenever the number of approving reviews is counted for the purpose of comparing against `required_approvals`, only those reviews which are up to date for the hash of the commit to be merged are counted.  If `up_to_date_approvals` is false, then all reviews from the PR are counted (maintaining current behavior).  The `up_to_date_approvals` option has no effect if `required_approvals` is not set.  The `up_to_date_approvals` option also does not affect the determination of whether or not there are any _rejecting_ reviews.

If a bors command is rejected due to not having enough approving reviews, then bors will include the number of reviews counted and the number of reviews required in its comment.  If `up_to_date_approvals` is true, it will also include the number of approving reviews that were not counted because they were not up to date.

Let _n_ be the value of `required_approvals`, _m_ be the number of total approvals present, and _l_ be the number of up to date approvals.

If `up_to_date_approvals` is false, or if _l_ == _m_:

> Rejected by too few approved reviews: _m_ out of _n_ required approvals were present

If `up_to_date_approvals` is true and _l_ != _m_:

> Rejected by too few approved reviews: _l_ out of _n_ required approvals were present (_m_ - _l_ stale reviews were not counted)

# Drawbacks

* Adds a new configuration option, increasing complexity.
* Might require more API calls to determine if reviews are up to date.
* UX may be confusing since GitHub will still display the stale reviews, but bors won't count them.

# Alternatives

* Make `up_to_date_approvals = true` the default behavior or the only behavior.
  * Not chosen because it would be a breaking change for current workflows
  * Not all users may want this stricter behavior, since it requires more effort from reviewers and change authors
* Use a CI check to enforce the number of up-to-date reviews
  * It's more impactful to make this change in bors instead of requiring every project that wants this behavior to implement it themselves.
  * Requires duplicating most of the existing functionality of `required_approvals` just to change one small aspect of it.
* Do nothing
  * For some projects it's not acceptable for code changes to be merged without a certain minimum number of reviews.

# Prior art

* The GitHub option, "Dismiss stale pull request approvals when new commits are pushed.", essentially implements the same behavior as this.
  * Note that projects using bors can't take advantage of this, because GitHub doesn't allow enabling this option without also enabling the "Require [nonzero number] of approving reviews" protection rule on the target branch.  This latter rule is itself [incompatible with bors](https://bors.tech/documentation/getting-started/#if-it-doesnt-work), since the merge commit created by bors counts as "unapproved".
* The Gerrit approval system automatically dismisses reviews when new code is pushed.
* graydon/bors' [implementation of code review](https://github.com/graydon/bors/blob/a6b07e6a876a312997eb241805bccb1b7899be95/bors.py#L321-L341) uses approval comments which are either attached to the commits themselves, or with the commit hash included in the comment.  Either way, approvals only count for a single commit.  This is the implementation used by the Rust language project.

# Unresolved questions

* What parts of the design do you expect to resolve through the RFC process before this gets accepted?
  * Final choice of configuration option name and exact wording of error messages
* What parts of the design do you expect to resolve through the implementation of this feature before deployment?
  * Details of interaction with the GitHub API to determine which reviews are stale.
* What related issues do you consider out of scope for this RFC that could be addressed in the future independently of the solution that comes out of this RFC?
  * Dismissal of stale rejecting reviews

# Future possibilities

Didn't think of anything.
