Summary: Bors-NG promises to support the current version of Elixir, plus the previous stable release, and the previous two Erlang versions.

> I allow this RFC document to be modified and redistributed under the terms of the [Apache-2.0 license](http://www.apache.org/licenses/LICENSE-2.0%3E), or the [CC-BY-NC-SA 3.0 license](http://creativecommons.org/licenses/by-nc-sa/3.0/deed.en_US), at your option.

# Motivation

While bors has been deliberately conservative with non-Mix dependencies, it's never had a written plan for them.

The purpose of supporting older versions of OTP/Elixir is to allow upgrading to be decoupled. They shouldn't have to upgrade everything at the same time. If their OS package manager hasn't approved the new version of OTP, but they're pulling bors from git, they can still stay up-to-date as long as their PM gets around to pushing the update before the next one comes out.

# Guide-level explanation

Bors will always be tested against the most recent, and second most recent, feature release of Elixir (feature releases are defined by the second number: 1.8.1 and 1.8.2 are the same feature version). The list of released versions of Elixir can be found at <https://github.com/elixir-lang/elixir/releases>. Bors will always test with the latest bugfix releases. For example, if 1.7.1, 1.7.2, 1.7.3, 1.7.4, 1.8.1, and 1.8.2 all exist, then bors will test against 1.7.4 and 1.8.2.

Bors will also be tested against the corresponding two most recent feature releases of OTP that are supported by the Elixir versions. For example, when this RFC was written, Bors will support OTP 22.0.1 and 21.3.8.2.

# Reference-level explanation

The set of Elixir versions we test against can be found, and updated, in <https://github.com/bors-ng/bors-ng/blob/ef4587809ee21600dbbcc6df127c9f6210aa2a1f/.travis.yml> and <https://github.com/notriddle/docker-phoenix-elixir-test>. The latter should probably be moved...

Elixir versions are currently not automatically bumped. Check <https://github.com/elixir-lang/elixir/releases> and <https://github.com/elixir-lang/elixir/releases> and <http://erlang.org/download/otp_versions_tree.html> for the versions to use.

# Drawbacks

It slows down development.

# Rationale and alternatives

* Our main underlying dependency, the Phoenix framework, seems to follow a similar policy. See <https://github.com/phoenixframework/phoenix/blob/fb3c92309c02cca1e4b3d857f8f19f27987078d0/.travis.yml> for their (somewhat wider) set of supported OTP and Elixir versions.
* The exact decision is based on a desire to avoid nonsense where you can't update bors because Chocolatey or Debian haven't gotten around to reviewing a new version of Elixir yet. These package managers tend to either never update, like Debian Stable, or never go more than one version behind upstream, like Debian Unstable or Chocolatey. I don't want to deal with the former, but I don't want people messed up by the latter, hence this choice.
* Alternatively, we could actually just pin ourselves to Phoenix ("we support every Elixir version that the corresponding version of Phoenix supports"). I'd like to hear a use case for it, though.

# Prior art

* These sorts of version pinning is so common, Travis CI has it built-in.
* An earlier version of this policy called the previous-stable version "oldstable", after Debian "oldstable", since Debian supports the immediately previous stable version for the same reason (the user needs a chance to upgrade).

# Unresolved questions

* What happens of some dependency can't handle it? Do we have to fork libraries? Or make an exception? (I vote for temporary forking, but I'm willing to be persuaded otherwise)

# Future possibilities

* Better guarantees and contracts between the deployment env and bors-ng.