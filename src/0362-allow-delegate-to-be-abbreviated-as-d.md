Summary: Add `bors d+` as an alias to `bors delegate+` and `bors d=someone` as an alias to `bors delegate=someone`.

> I allow this RFC document to be modified and redistributed under the terms of the [Apache-2.0 license](http://www.apache.org/licenses/LICENSE-2.0), or the [CC-BY-NC-SA 3.0 license](http://creativecommons.org/licenses/by-nc-sa/3.0/deed.en_US), at your option.

# Motivation

Based on @tommilligan's pull request and @matklad's issue, a lot of people are annoyed by how long the delegate command's name is.

# Guide-level explanation

In addition to adding reviewers who can approve any PR in the repo, you can “delegate” permission to approve a single PR to anyone else. It works like this:

        @some-user: bors r+
        @bors[bot]: Permission denied
        @some-reviewer: bors d=some-user
        @bors[bot]: some-user now has permission to review this pull request.
        @some-user: bors r+
        @bors[bot]: Added to queue

If some-user happens to be the pull request author, you can also use the shorthand `d+` command.

# Reference-level explanation

|Syntax |Description|
|---|---|
| `bors delegate+` |Allow the pull request author to r+ their changes.|
| `bors delegate=[list]` |Allow the listed users to r+ this pull request’s changes.|
| `bors d+` |Allow the pull request author to r+ their changes (same as `delegate+`).|
| `bors d=[list]` |Allow the listed users to r+ this pull request’s changes (same as `delegate=[list]`).|

# Drawbacks

It adds another command that we need to maintain.

It's also less readable, though, since bors responds with a message with what you're doing, that should be fine. Also, we already allow the super-short and kind of opaque `r+`, so it doesn't make much sense to be picky about self-documenting commands.

# Rationale and alternatives

* Why is this design the best in the space of possible designs?

  It fits with the way `r+` and `r=` work.

* What other designs have been considered and what is the rationale for not choosing them?

  About the only one that's really been considered is leaving it with `delegate`.

* What is the impact of not doing this?

  It's kind of annoying for people who regularly use delegation.

# Prior art

No known prior art for this specifically. As an interesting note, the reason why the command keyword is `bors` instead of `bors-ng`, or the older name of this codebase, `aelita2`, is for this same reason, so there's lots of precedent for using short command names in bors.

# Unresolved questions

The "design" is pretty much already nailed down.

# Future possibilities

`ping` is extremely rare, and pretty short anyway, so there's no real point in abbreviating it. We also couldn't use `p` anyway, since that's already taken for priority adjustment.

The only other multi-letter commands are `try` and `retry`. Try is pretty short, at three letters, and retry ought to be pretty rare, even if not as rare as ping.

