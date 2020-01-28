Summary: Clean up GitHub Issue labels.

<!-- RFC documents are put under a dual license, because the default license for content on this forum is CC-BY-NC-SA, while the license for bors's code is Apache 2.0 -->

> I allow this RFC document to be modified and redistributed under the terms of the [Apache-2.0 license](http://www.apache.org/licenses/LICENSE-2.0), or the [CC-BY-NC-SA 3.0 license](http://creativecommons.org/licenses/by-nc-sa/3.0/deed.en_US), at your option.

# Motivation

There are a couple of big problems with them.

* They're not consistently set. Only people with access to the repository can add them (which is mostly just me, plus a few other regulars). The amount of work required to maintain them, however, should be minimized if possible.

* They're not the same as what other stuff uses. GitHub treats the `good first issue` label a special, but knows nothing about `E-easy`, which predates it. Dependabot uses plain old `elixir` and `javascript` labels, which are redundant with `L-elixir` and `L-javascript`. Doing a bunch of bespoke configuration is annoying, and it would be better to integrate with others' defaults.

* They're too complicated. The only labels that anyone really cares about are something like "issue accepted", the language labels, E-easy, and maybe a high priority ones. The other schemata is not necessary.

# Guide-level explanation

We should change this to just be a redirect:

https://bors.tech/starters/ ➡ https://github.com/bors-ng/bors-ng/contribute

Also:

> If you're looking for an issue to work on, look for the `good first issue`, `mentored`, and `help wanted` issues, indicating that any pull requests fixing the issue will definitely be merged.

# Reference-level explanation

* `high priority` — indicates an issue that impacts a lot of existing users, has security implications, or otherwise needs done NOW
* `blocked` — indicates a pull request that should not be merged, or an issue that cannot be solved yet
* `javascript` — indicates an issue or pull request that primarily relies on JavaScript code (sometimes added by dependabot to pull requests)
* `elixir` — indicates an issue or pull request that primarily relies on Elixir code (sometimes added by dependabot to pull requests)
* `css` — indicates an issue or pull request that primarily relies on CSS code
* `html` — indicates an issue or pull request that primarily relies on HTML and IEX code
* `good first issue` — indicates that the fix should be easy, self-contained, and that someone is ready to guide you through fixing it
* `mentored` — indicates that someone is ready to guide you through fixing it
* `help wanted` — indicates that the issue is definitely a bug or accepted feature
* `dependencies` — indicates that a pull request updates dependencies (added by dependabot to pull requests)
* `duplicate` — indicates that an issue or pull request was redundant

# Drawbacks

This does remove a few labels, particularly the ones distinguishing between feature requests and bugs. This is mostly to avoid pointless categorization between the two, but it does remove some help with prioritizing.

# Rationale and alternatives

* This design is mostly modeled after what repositories *other than Rust* do. In particular, several of the labels are based on the ones that Dependabot and GitHub treat specially, so that we get better integration with them.

  * https://help.github.com/en/github/building-a-strong-community/encouraging-helpful-contributions-to-your-project-with-labels
  * https://help.github.com/en/github/managing-your-work-on-github/about-labels

* We could, alternatively, keep the `bug` and `feature` labels, if anybody actually uses them.

* [Dependabot only seems capable of replacing the `dependencies` label, not of actually changing the language labels](https://dependabot.com/docs/config-file/), so making it use the `L-` labels seems impossible.

# Prior art

Bazillions of them. Here's some I knew about and had in mind.

* https://github.com/phoenixframework/phoenix/labels
* https://github.com/rust-lang/rust/labels
* https://github.com/elixir-lang/elixir/labels
* https://github.com/SergioBenitez/Rocket/labels
* https://github.com/libressl-portable/portable/labels
* https://github.com/lobsters/lobsters/labels
* https://github.com/github/hub/labels

Also, I expect a decent number of users to learn through GitHub's documentation, so let's try to follow their recommendations wherever possible:

* https://opensource.guide/

# Unresolved questions

We should probably look at the other repos in more detail. I would really like some numbers:

* How often are particular labels sorted on?

* How often are particular labels applied?

* How are labels used? For dev priorities? For newbie discovery?

# Future possibilities

I can't think of any.

