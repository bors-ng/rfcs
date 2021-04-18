Summary: Add an endpoint that can be used for simple health checking. This endpoint should always return an HTTP 200 OK response code as long as the server is able to serve traffic.

<!-- RFC documents are put under a dual license, because the default license for content on this forum is CC-BY-NC-SA, while the license for bors's code is Apache 2.0 -->

> I allow this RFC document to be modified and redistributed under the terms of the [Apache-2.0 license](http://www.apache.org/licenses/LICENSE-2.0), or the [CC-BY-NC-SA 3.0 license](http://creativecommons.org/licenses/by-nc-sa/3.0/deed.en_US), at your option.

# Motivation

Support load balancers, proxies, and other services that implement some form of health checks without requiring workarounds like treating redirects as healthy or bypassing the health checks altogether. Some systems don't allow the above workarounds, and cannot be used. This health check endpoint is not meant to display any details about the status of Bors features or the database, simply provide a way for services to verify the underlying Elixir server is ready to handle requests.

# Guide-level explanation

The `/health` endpoint can be used to verify the Elixir server is running and ready to accept requests.

# Reference-level explanation

Added endpoint(s):

* `/health` - will always return an HTTP 200 OK response code. This endpoint does not imply that all features are functioning as expected, instead it simply states the Elixir server powering Bors is running and ready to handle requests. 

# Drawbacks

It could be misleading to have a health check endpoint that returns HTTP 200s when things internally might be broken. It's also an additional endpoint, albeit a simple one, that will require maintenance and consideration moving forward.

# Rationale and alternatives

Not really sure of any alternatives here besides bypassing health checks altogether, or updating specific load balancers/proxies to support treating redirect behaviors as healthy but that would be significantly more difficult and in some cases just outright impossible for hosted service like GCP or AWS.

# Prior art

It seems this essentially does what we need, however it's behind a redirect and is specific to webhooks.
https://github.com/bors-ng/bors-ng/blob/08a938d08bdad80b84199d99511f62479be6d012/lib/web/controllers/webhook_controller.ex#L90-L92

# Unresolved questions

I've assumed the endpoint would live at `/health`, but am open to considering different routes. 

# Future possibilities

This health check endpoint could theoretically be expanded in the future to support reporting data for Bors internals, such as queue size.

# See also

If this RFC already has a draft Pull Request, link to it here:

Tracking issue: https://github.com/bors-ng/bors-ng/issues/1192
