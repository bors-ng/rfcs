Summary: Keep track of when a pull request is unmergeable, or a draft, and reject them.

> I allow this RFC document to be modified and redistributed under the terms of the [Apache-2.0 license](http://www.apache.org/licenses/LICENSE-2.0), or the [CC-BY-NC-SA 3.0 license](http://creativecommons.org/licenses/by-nc-sa/3.0/deed.en_US), at your option.

# Motivation

Right now, bors checks whether a pull request is "not mergeable" according to GitHub's API, before it tries to merge it. This is good, but it's kinda late at that point. The pull request webhook actually [reports whether it's mergeable in advance](https://docs.github.com/en/developers/webhooks-and-events/webhook-events-and-payloads#webhook-payload-object-31), so if bors used that, it would be able to reject it earlier and avoid wasting time, and it could show that information on the pull request dashboard.

# Guide-level explanation

This change is almost completely transparent to the user, and would basically only change the behavior in very weird corner cases, like if they queued a pull request that wouldn't merge cleanly into the main branch behind another one that actually fixed the merge conflict.

# Reference-level explanation

Bors will check whether your pull request is mergeable both before enqueueing it, and after dequeueing it.

Bors will also refuse to merge draft pull requests.

# Drawbacks

It adds two new columns to the database, and one new column to the dashboard.

# Rationale and alternatives

* By adding it to the database, bors can also show it in the dashboard, which makes it a bit more useful (whether a pull request is mergeable or not seems important).
* The current one uses slightly less code, but it's probably not what we want.

# Prior art

* It's the design that [homu uses](https://bors.rust-lang.org/queue/rust).

# Unresolved questions

I'm not really aware of any.

# Future possibilities

Right now, we don't pluck unmergeable pull requests out of batches, but probably could change to do that. I don't think it would actually make much difference, performance-wise, but it would make the dashboard a bit more accurate.

# See also

* Implemented by <https://github.com/bors-ng/bors-ng/pull/1159>

## Screenshot

![image|690x261](https://forum.bors.tech/uploads/default/original/1X/a0744e21768ae50d6c03cfbd74e8f0febbbbc418.png)

![image|690x482](https://forum.bors.tech/uploads/default/original/1X/5ddb5f1299dbad9c251040ba704342f405fe8320.png)

![image|502x500](https://forum.bors.tech/uploads/default/original/1X/f499f1214c7a9aca6208eeca94bffb7dda024ec2.png)