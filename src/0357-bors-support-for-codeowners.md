Summary: Add an option to have bors parse and enforce the GitHub-designed `CODEOWNERS` file.

> I allow this RFC document to be modified and redistributed under the terms of the [Apache-2.0 license](http://www.apache.org/licenses/LICENSE-2.0), or the [CC-BY-NC-SA 3.0 license](http://creativecommons.org/licenses/by-nc-sa/3.0/deed.en_US), at your option.

# Motivation

Currently, bors only supports requiring numbers of contributors.  This works for many use cases but several users need the power of GitHub [CODEOWNERS](https://help.github.com/en/articles/about-code-owners).  CODEOWNERS let users set a 'required' code reviewer for specific code.  Eg, you could require review by security team if code in the security repository is altered.

# Guide-level explanation

This feature is intended to exactly mimic the functionality GitHub provides with their CODEOWNERS implementation.  As such, the exact guide level docs are owner by GitHub([here](https://help.github.com/en/articles/about-code-owners))

For bors, CODEOWNERS file support will be enabled by setting `enable_codeowners = true` in the bors.toml file.

# Reference-level explanation

Please see Github official [docs](https://help.github.com/en/articles/about-code-owners)

# Drawbacks

This will somewhat complicate the system by requiring us to follow the CODEOWNERS file format instead of just counting required reviewers.

# Rationale and alternatives

This is an important tool to support the Bors Squash merge feature.  Many teams cannot switch to squash-merge if they would lose the CODEOWNERS functionality.

An alternative, this could be implemented by an external github tagger bot.  The bot could label PRs that still need review and could block on the attached label.

# Prior art

* GitHub - This feature is widely used in larger organizations.

# Unresolved questions

* Should the feature simply be automatically enabled when a CODEOWNERS file is present?

# Future possibilities

We could extend the CODEOWNERS functionality to support features not officially supported in GitHub proper.
