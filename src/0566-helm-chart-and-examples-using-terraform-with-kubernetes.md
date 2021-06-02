Summary: Provide a Helm chart to facilitate the adoption of bors on a self-hosted manner

<!-- RFC documents are put under a dual license, because the default license for content on this forum is CC-BY-NC-SA, while the license for bors's code is Apache 2.0 -->

> I allow this RFC document to be modified and redistributed under the terms of the [Apache-2.0 license](http://www.apache.org/licenses/LICENSE-2.0), or the [CC-BY-NC-SA 3.0 license](http://creativecommons.org/licenses/by-nc-sa/3.0/deed.en_US), at your option.

# Motivation

As part of a company initiative to improve our security and manage better our costs we have moved our `bors-ng` deployment from Heroku into one of our private Kubernetes clusters. As part of that effort we have experimented and generated some Terraform code and Helm charts to achieve the same goal which is getting `bors-ng` running on a Kubernetes cluster. Since all this effort is not bounded in any way to the company we have decided to donate these Terraform snippets and Helm chart to `bors-ng` since we are benefitting from the application.

This is specially useful for operator (like myself)

# Guide-level explanation

The code to add is not related to the application `bors-ng` on per se but the way it's run on the container orchestrator Kubernetes. A PR (link at the bottom) has already been created. The code included on the PR contains:

- Helm chart v2 (Helm 3 compatible only)

- Terraform snippet to deploy on Kubernetes using `kubernetes` provider

- Terraform snippet to deploy on Kubernetes using `helm` provider and the chart included here

# Reference-level explanation

As explained before this RFC does not affects the `bors-ng` code instead adds a couple of ways to easily deploy `bors-ng` on Kubernetes using some of the most popular tooling to manage infrastructure and Kubernetes

# Drawbacks

* More code to maintain

`¯\_(ツ)_/¯`

# Rationale and alternatives

* Why is this design the best in the space of possible designs?

* What other designs have been considered and what is the rationale for not choosing them?

* What is the impact of not doing this?

# Prior art

* Heroku one click deployment

* `docker` cli command to run bors on a container

# Unresolved questions

* Publishing the chart on a Helm repository. An option has been suggested on the PR mentioned below using Helm chart releaser and Github Pages making the hosting free and automated

# Future possibilities

* Adding `bors-ng` Helm repository to CNCF Artifacthub (searchable index for many Helm chart repositories)

* Helm chart compatible with Openshift

* Terraform modules addded to the hashicorp registry

* This could help to improve `bors-ng` adoption

# See also

* Implemented by <https://github.com/bors-ng/bors-ng/pull/1209>

* "Add simple health check" <https://forum.bors.tech/t/add-a-simple-health-check-endpoint/563/3>