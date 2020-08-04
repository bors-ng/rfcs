Summary: Add `bors merge` as an alias to `bors r+`, `bors merge=FOO` as an alias to `bors r=FOO`, `bors merge p=123` as an alias to `bors r+ p=123`, `bors merge=FOO p=123` as an alias to `bors r=FOO p=123`, and `bors merge-` as an alias to `bors r-`.

> I allow this RFC document to be modified and redistributed under the terms of the [Apache-2.0 license](http://www.apache.org/licenses/LICENSE-2.0), or the [CC-BY-NC-SA 3.0 license](http://creativecommons.org/licenses/by-nc-sa/3.0/deed.en_US), at your option.

# Motivation

Based on the discussion in a previous forum thread (https://forum.bors.tech/t/user-defined-aliases-for-bors-commands/371), multiple users are confused by the `bors r+` command.

# Guide-level explanation

Once you've set it up, instead of clicking the green "Merge Button",
leave a comment like this on the pull request:

> bors r+

Equivalently, you can comment the following

> bors merge

The pull request, as well as any other pull requests that are reviewed around the same time, will be merged into a branch called "staging". Your CI tool will test it in there, and report the result back where Bors-NG can see it. If that result is "OK", master gets fast-forwarded to reach it.

The status can be seen in the Dashboard page, which also makes a good one-stop-shop to see pull requests that are waiting for review.

# Reference-level explanation

| Syntax | Description |
|--------|-------------|
| bors r+ | Run the test suite and push to master if it passes. Short for "reviewed: looks good."
| bors merge | Equivalent to `bors r+`
| bors r=[list] | Same as r+, but the "reviewer" in the commit log will be recorded as the user(s) given as the argument.
| bors merge=[list] | Equivalent to `bors r=[list]`
| bors r- | Cancel an r+ or r=.
| bors merge- | Equivalent to `bors r-`
| bors p=[priority] | Set the priority of the current pull request. Pull requests with different priority are never batched together. The pull request with the bigger priority number goes first.
| bors r+ p=[priority] | Set the priority, run the test suite, and push to master (shorthand for doing p= and r+ one after the other).
| bors merge p=[priority] | Equivalent to `bors r+ p=[priority]`

# Drawbacks

It adds another command that we need to maintain.

It creates multiple ways of doing the same thing, which might lead to some confusion.

# Rationale and alternatives

Why is this design the best in the space of possible designs?
- It allows for consistency across different projects that use Bors-NG.

What other designs have been considered and what is the rationale for not choosing them?
- We could allow unlimited customization of aliases in `bors.toml`. But then this will lead to inconsistency across different projects that use Bors-NG and will become a real headache for people that are reviewers on multiple different projects.

What is the impact of not doing this?
- If we do nothing, then people will continue to be confused by the opaque `r+` command.

# Prior art

The prior art for adding aliases to Bors-NG is the following RFC: https://forum.bors.tech/t/allow-delegate-to-be-abbreviated-as-d/362

# Unresolved questions

None.

# Future possibilities

Hopefully we won't need to add any more aliases after this one.

# Implementation status

**Implemented**:

* Pull request: https://github.com/bors-ng/bors-ng/pull/746

