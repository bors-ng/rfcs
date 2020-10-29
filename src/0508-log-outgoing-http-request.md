Summary: Add a config option to log all outgoing HTTP requests.

<!-- RFC documents are put under a dual license, because the default license for content on this forum is CC-BY-NC-SA, while the license for bors's code is Apache 2.0 -->

> I allow this RFC document to be modified and redistributed under the terms of the [Apache-2.0 license](http://www.apache.org/licenses/LICENSE-2.0), or the [CC-BY-NC-SA 3.0 license](http://creativecommons.org/licenses/by-nc-sa/3.0/deed.en_US), at your option.

# Motivation

This is a debugging aid, which is why it'll be off by default.

# Guide-level explanation

If you set the `BORS_LOG_OUTGOING` environment variable, it will write every HTTP request to the log, making it easier to debug the GitHub API usage. For example

    2020-08-03T07:00:15.168988+00:00 app[web.1]: 07:00:15.168 pid=<0.344.0> [info] GET https://api.github.com/repositories/284424834/contents/bors.toml -> 200 (194.823 ms)
    2020-08-03T07:00:15.420605+00:00 app[web.1]: 07:00:15.420 pid=<0.344.0> [info] GET https://api.github.com/repositories/284424834/issues/9/labels -> 200 (226.039 ms)
    2020-08-03T07:00:15.621675+00:00 app[web.1]: 07:00:15.621 pid=<0.344.0> [info] GET https://api.github.com/repositories/284424834/commits/5a99b398b8c1d16f1ba5120b6670fdb31903c38f/status -> 200 (200.326 ms)
    2020-08-03T07:00:16.079808+00:00 app[web.1]: 07:00:16.079 pid=<0.344.0> [info] GET https://api.github.com/repositories/284424834/commits/5a99b398b8c1d16f1ba5120b6670fdb31903c38f/check-runs -> 200 (457.674 ms)
    2020-08-03T07:00:16.286852+00:00 app[web.1]: 07:00:16.286 pid=<0.344.0> [info] GET https://api.github.com/repositories/284424834/commits/5a99b398b8c1d16f1ba5120b6670fdb31903c38f/status -> 200 (206.264 ms)
    2020-08-03T07:00:16.468819+00:00 app[web.1]: 07:00:16.467 pid=<0.344.0> [info] GET https://api.github.com/repositories/284424834/commits/5a99b398b8c1d16f1ba5120b6670fdb31903c38f/check-runs -> 200 (180.530 ms)
    2020-08-03T07:00:16.468828+00:00 app[web.1]: 07:00:16.467 pid=<0.547.0> [info] Checking code owners
    2020-08-03T07:00:16.667539+00:00 app[web.1]: 07:00:16.666 pid=<0.344.0> [info] GET https://api.github.com/repositories/284424834/contents/.github/CODEOWNERS -> 200 (198.168 ms)
    2020-08-03T07:00:16.687920+00:00 app[web.1]: 07:00:16.686 pid=<0.547.0> [info] CODEOWNERS file %BorsNG.CodeOwners{patterns: [%BorsNG.FilePattern{approvers: ["@organisationjetteauloin/dummy"], file_pattern: "*"}]}

# Reference-level explanation

The `BORS_LOG_OUTGOING` environment variable will activate the [`Tesla.Middleware.Logger`](https://hexdocs.pm/tesla/Tesla.Middleware.Logger.html) plug-in.

# Drawbacks

It's probably not going to be used that much, though the ones that do use it will love it.

# Rationale and alternatives

* This could be turned on by default, instead of requiring a config option, but it's not likely to be used much in the production instances, because it's very noisy.
* This could be done using a different method than the Elixir logger, but maybe metrics gathering should be a separate matter anyway.

# Prior art

This is based on [Tesla's existing features](https://hexdocs.pm/tesla/Tesla.Middleware.Logger.html). We're just exposing it to the user.

# Unresolved questions

Do we want something this ad-hoc? Or would we rather come up with more of an all-encompassing plan?

# Future possibilities

Collecting metrics on API calls could allow us to detect things that go wrong, even in production setups where "just log and read everything" isn't much of an option.

# See also

* Mostly implemented by <https://github.com/bors-ng/bors-ng/pull/996>

# Implementation status

* in progress
