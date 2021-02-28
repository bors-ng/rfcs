Summary: make database timeout configurable

<!-- RFC documents are put under a dual license, because the default license for content on this forum is CC-BY-NC-SA, while the license for bors's code is Apache 2.0 -->

> I allow this RFC document to be modified and redistributed under the terms of the [Apache-2.0 license](http://www.apache.org/licenses/LICENSE-2.0), or the [CC-BY-NC-SA 3.0 license](http://creativecommons.org/licenses/by-nc-sa/3.0/deed.en_US), at your option.

# Motivation

Depending on the way bors is deployed, the default timeout might be too short.

# Guide-level explanation

If your deployment requires it, you can change the database timeout, such as in your docker-compose.yml 

    DATABASE_TIMEOUT: 60000

# Reference-level explanation

The new environment variable:

- `DATABASE_TIMEOUT` may be set higher than the default of `15_000`(ms). This may be necessary with repositories with a very large amount of members.

We recommend also trying to make sure you have a high POOL_SIZE if you’re going to do this, so that other queries don’t get blocked, and also using a slow query log to try to find and optimize whichever query or transaction is taking so long.

# Drawbacks

It might just be masking a problem, such as a missing index or a transaction that’s blocking on an API call.

# Rationale and alternatives

It’s pretty much just exposing Ecto’s timeout setting. I’m not sure how else it would work.

We could also try to avoid hitting the lower timeout by modifying the code, or by dedicating a better database server to it, but that requires lots of work and profiling.

# Prior art

Basically everybody exposes this kind of configurable knob. It’s weird that bors-ng didn’t.

# Unresolved questions

Beyond bikeshedding the name? Nothing really.

# Future possibilities

We expose API and database timeouts. How about incoming HTTP timeouts?

# See also

* Implemented by <https://github.com/bors-ng/bors-ng/pull/1134>
