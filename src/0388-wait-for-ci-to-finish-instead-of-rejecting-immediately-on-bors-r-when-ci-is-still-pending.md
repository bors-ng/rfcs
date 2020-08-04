Summary: We distinguish between Github statuses that are errors and waiting differently (instead of treating them all as a failed `passed_status` . In the case where there are no errors and some waiting statuses, notify with a response and wait polling with an exponential backoff until we're in some other state than "no errors and some waiting statuses" (i.e., `patch_preflight` returns something else).

> I allow this RFC document to be modified and redistributed under the terms of the [Apache-2.0 license](http://www.apache.org/licenses/LICENSE-2.0), or the [CC-BY-NC-SA 3.0 license](http://creativecommons.org/licenses/by-nc-sa/3.0/deed.en_US), at your option.

# Motivation

When using the `pr_status` feature to filter allowed pull requests, it really makes more sense to actually wait for it to finish, rather than immediately erroring. This process can also time out, so it is also necessary to expose a configuration option for changing that timeout.

# Guide-level explanation

There are two ways to gate on a GitHub Status CI report. The regular `status` option is used to watch for CI runs on the `staging` branch. The other, `pr_status`, is used to watch for integrations that only run on pull requests. Bors will wait for the PR status to complete before it merges into the staging branch.

# Reference-level explanation

Change the documentation for `pr_status`. Also add documentation for this configuration option.

| Name | Description |
|--|--|
| `pr_status` | List of commit statuses that must pass on the PR commit <s>when it is r+-ed</s> *before it will go into staging*. If it is still running, bors will wait. If it fails, bors will fail immediately. |
| `prerun_timeout_sec` | Timeout while waiting for `pr_status` checks to finish. |

# Drawbacks

Other than code complexity, there are really no user-facing reasons to avoid doing this. It's obviously better in the cases where a PR status is still running. There are no known cases where users would prefer the old behavior, really.

# Rationale and alternatives

* Not doing this is a pretty terrible option that got a lot of complaints before someone eventually got around to implementing it.
* As for doing this with a different setup, the obvious alternative is to add a separate config option for "insta-fail statuses" and "waited statuses". If anybody wants that, let me know!

# Prior art

* Homu doesn't seem to have anything even remotely comparable. The `pr_context` config option that they have never triggers waits. It triggers bypass behavior.
* Zuul seems to work the same way this RFC and its corresponding PR behave, though I can't tell for sure from their docs. [They have a bunch of different kinds of triggers in place, so it might really be project-specific](https://zuul-ci.org/docs/zuul/user/concepts.html). Their dependency infrastructure definitely seems sophisticated enough to implement this RFC, though.

# Unresolved questions

* The question that came to my mind, that I'd like to know about, is the one described in the "alternatives" section: **does anybody have any status changes that they don't want bors to ever wait on?** That is, does anyone actually prefer the old behavior?

# Future possibilities

There are a bunch of pre-checks. Right now, we only poll statuses, but maybe we'd want to poll some of the others? Even if it's only with super-short timeouts to try to work around potential race conditions.

# See also

* Implemented by <https://github.com/bors-ng/bors-ng/pull/785>

# Implementation status

**Implemented**:

* Pull request: https://github.com/bors-ng/bors-ng/pull/785

