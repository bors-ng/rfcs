This RFC proposes a new environment variable,  `PUBLIC_PROTOCOL`. If unset, or set to `https`, the behaviour of bors will remain unchanged, and the bors app will be served at a HTTPS endpoint. If set to `http`, the bors app will be served at a HTTP endpoint.


<!-- RFC documents are put under a dual license, because the default license for content on this forum is CC-BY-NC-SA, while the license for bors's code is Apache 2.0 -->

> I allow this RFC document to be modified and redistributed under the terms of the Apache-2.0 license, or the CC-BY-NC-SA 3.0 license, at your option.

# Motivation

Allow the bors app to run at a HTTP endpoint if HTTPS is not required or available.

Specifically, I have a use case at work to serve the  `bors`  app at a HTTP endpoint, which will be connected to securely by other means (SSH port forwarding).

Currently, the `PUBLIC_HOST` is assumed to be served over HTTPS (and delivers  `301`  redirects accordingly). This makes it impossible to expose the app at a HTTP address.

```
12:46:59.521 pid=<0.2186.0> [info] Plug.SSL is redirecting GET / to https ://172.17.0.1:39103 with status 301
```

This also makes it hard to test changes locally, without proxying via another service acting as a TLS terminator (such as nginx).

# Guide-level explanation

A new environment variable `PUBLIC_PROTOCOL` will be added, to match the existing variables `PUBLIC_PORT` and `PUBLIC_HOST`.

This can be left unset, or set to one of `https`, `http`. The behaviour of `https` and unset are identical, and consistent with current behaviour.

If set to `http`, the bors app will be served at a HTTP endpoint.

Please see the proposed implementation (link below)

# Reference-level explanation

No further detail.

# Drawbacks

This gives users the option to expose bors at a HTTP endpoint. This conceivably makes it easier for users to expose such a deployment insecurely. However, this feature is opt it, requiring a user to consciously consider their choice.

# Rationale and alternatives

This design is back compatible, and allows more flexibility when choosing to deploy bors.

If bors does not have an option to serve the dashboard over HTTP, this makes deploying bors strictly harder in some scenarios.

# Prior art

It is common for developer-oriented dashboards and internal access points to be exposed over HTTP, with HTTPS an external concern.

For instance:
- CockroachDB Dashboard: https://www.cockroachlabs.com/docs/v20.1/admin-ui-overview#admin-ui-access
- Elasticsearch's Kibana: https://www.elastic.co/guide/en/kibana/current/access.html#access

# Unresolved questions

n/a

# Future possibilities

n/a

# See also

* Implemented by https://github.com/bors-ng/bors-ng/pull/1043
* Issue raised at https://github.com/bors-ng/bors-ng/issues/1042

