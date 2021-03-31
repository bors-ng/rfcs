Summary: Add "bors cancel" as alias for "bors r-"

> I allow this RFC document to be modified and redistributed under the terms of the [Apache-2.0 license](http://www.apache.org/licenses/LICENSE-2.0), or the [CC-BY-NC-SA 3.0 license](http://creativecommons.org/licenses/by-nc-sa/3.0/deed.en_US), at your option.

# Motivation

It's just readability of the chat history.

# Guide-level explanation

Use `bors cancel` to cancel a PR from the queue.

# Reference-level explanation

| command | description |
|--|--|
| bors cancel | Equivalent to bors r- |

# Drawbacks

We already have one synonym. There's not much reason not to have two.

# Rationale and alternatives

Custom commands? No way. It's hard enough making sure everyone understands what's going on.

# Prior art

* `bors merge-`

# Unresolved questions

* What's the cut-off? We don't want to enable every possible one. `cancel` seems alright, because other parts of the app call it "canceling," but if someone proposes "stop," we might want to say no.

# Future possibilities

Since we already refer to it as "cancel"ing in other places, this makes sense, but beyond that, there hopefully won't be any more of these.

# See also

* Implemented by <https://github.com/bors-ng/bors-ng/pull/1191>