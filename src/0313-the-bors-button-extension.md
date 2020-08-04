Summary: Commit to maintaining an extension that adds a `bors r+` button to GitHub, move it to the bors-ng GitHub org, and publish it to as many extension stores as possible.

> I allow this RFC document to be modified and redistributed under the terms of the [Apache-2.0 license](http://www.apache.org/licenses/LICENSE-2.0), or the [CC-BY-NC-SA 3.0 license](http://creativecommons.org/licenses/by-nc-sa/3.0/deed.en_US), at your option.

# Motivation

One of the common complaints about bors-ng is that the command is a pain to type and annoying to remember. There aren't very many good ways to make it easier that don't have significant drawbacks, but what we could do is add a button to the user interface that approves something in bors instead of requiring the user to type it out.

# Guide-level explanation

To make using bors with GitHub Reviews easier, you can install the *bors button* extension. This adds a button to the GitHub Reviews interface to approve-and-merge.

Starting out, this extension defaults to assuming bors-ng is used in its default configuration, but also has a hardcoded list of projects that use instances of homu or bors-ng with customized command names instead. Improving how it figures that out in the future would make a *great* pull request, but for now, if you're using a setup with bors-ng or homu and the `bors r+` button doesn't work for you, you can also open a pull request to add your project to the hardcoded list.

If you have permission to do so, the `bors r+` button makes an Approving GitHub Review with `bors r+` in it, so not only is bors triggered to run, it also gives that green checkmark to your contributor, which is comforting when they're not familiar with your workflow.

# Reference-level explanation

The WebExtension is maintained by a small group of people called the "webextension keyholders." They share access to accounts in every applicable browser extension store (like the Microsoft Edge one, the Google Chrome one, the Apple Safari one, and the Mozilla Firefox one). Adding the bors extension to another store requires there to be at least one keyholder who primarily uses that browser, to make sure it's getting tested in it.

The keyholders team will decide where those keys should be stored. Probably, 1password, keybase, or something.

# Drawbacks

This is kind of tangential to the bors-ng project itself. Especially since we're committing to supporting homu users with this extension.

# Rationale and alternatives

* On the other hand, since we're supporting bors-ng users who reassign their bot name, we already have to have the whitelist, since there's no way to detect what the trigger word is for any particular repository anyway. We could add an API, but that's far more work, and this way seems fine as long as there aren't that many bors-ng instances that change the word.
* The impact of doing nothing is that we can't offer an option to people who hate the command interface. They have been, and continue to, ask for ways around it.

# Prior art

* This is not the only extension for GitHub. There's plenty of them, like [ZenHub] and [GitHub Extended]. I'm not aware of any CI apps that do this, though.

[ZenHub]: https://www.zenhub.com/
[GitHub Extended]: https://github.com/onmyway133/github-extended

* Since this is a trivial convenience improvement for the existing app, it should easily fit in with the existing bors-ng workflow. From the point of view of someone not doing the reviewing, nothing should change at all.

* As the keyholders group, the concept of using a single user account with a shared password in a password manager is copied directly from what Mozilla does.

# Unresolved questions

* I'd like to gather together everyone who will be in the extension keyholders group. There are a lot of accounts associated with bors, and it would be nice if we could trial the keyholders' process structure with something that isn't critical infrastructure.

* Which browsers will be initially supported depends on who I can convince to help test it.

# Future possibilities

Designing an API that we can use instead of the hardcoded list should be done as its own RFC.

# Implementaton status

**Implemented**:

* Source code: https://github.com/bors-ng/bors-extension

* Firefox add-on: https://addons.mozilla.org/en-US/firefox/addon/bors-for-github-com/

