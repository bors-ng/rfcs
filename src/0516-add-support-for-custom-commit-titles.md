Summary: Introduce a new configuration property to define a commit title template.

> I allow this RFC document to be modified and redistributed under the terms of the [CC-BY-NC-SA 3.0 license](http://creativecommons.org/licenses/by-nc-sa/3.0/deed.en_US).

# Motivation

My team uses the conventional commits specification (conventionalcommits .org) for our commit messages and we use GitCop (gitcop .com) to police that users adhere to these rules. We also only merge PRs using Bors, but Bors's hardcoded commit titles do not comply to the same rules.

I would like to  have a way for us to specify what the commit title should look like when Bors creates a merge commit. This feature has also been mentioned in issue 637 and discussed in https://forum.bors.tech/t/how-to-change-the-commit-message-format/307.

# Guide-level explanation

I propose to introduce a new configuration property: `commit_title`.

The `commit_title` configuration property can be used to define a custom template for the commit title, that Bors applies to generate the commit message of new merge commits. The `commit_title` can be configured in your `bors.toml` configuration file.

For example, let's say you'd like Bors to merge all PRs with the commit title `merge pull request`. Simply add the following to your `bors.toml` configuration file.

```toml
commit_title = "merge pull request"
```

It is also possible to refer to the PR's unique identifier using a reserved keyword in the `commit_title`.

For example, let's say you'd like Bors to merge a PR with id #8 using the commit title `merge: #8`. Just add the following to your `bors.toml` configuration file.

```toml
commit_title = "merge: ${PR_REFS}"
```

If you do not specify a `commit_title`, it defaults to:

```toml
commit_title = "Merge ${PR_REFS}"
```

# Reference-level explanation

The `commit_title` is defined in the following BNF:

```bnf
<commit_title> ::= <segment> | <commit_title> <segment>
<segment> ::= <prefix> <pr_refs> <suffix>
<prefix> ::= "" | <string>
<pr_refs> ::= "" | "${PR_REFS}"
<suffix> ::= "" | <string>
<string> ::= ... # any non empty string
```

This grammar means that the `commit_title` can contain:
- any non-empty string
- as many `${PR_REFS}` references as you'd like, and in any index in the string

All occurrences of the `${PR_REFS}` keyword are replaced by the string of pr references. This is generated in the following manner:
```
For every PR that will be merged with this commit:
   take the identifier (i.e. `xref`)
   prepend the char `#`
   join with space char in between
```
For the set of PR identifiers `[1,2,3,4]` this would result in: `#1 #2 #3 #4`.

# Drawbacks

Why should we not do this?

* It adds an additional config property and a reserved keyword to maintain.
* It might lead to unforeseen bugs.

# Rationale and alternatives

* Why is this design the best in the space of possible designs?
It allows for flexibility and is backwards compatible
* What other designs have been considered and what is the rationale for not choosing them?
I have not yet considered other designs
* What is the impact of not doing this?
Some users might feel they can't use a tool like Bors if they can't have this feature, although unlikely.

# Prior art

* For feature RFC's: What other CI/CD systems implement anything similar to this one? What do they do well? What do they do poorly? If this proposal is similar to what one of them already does, did you change anything, and why or why not? Why would the user pick bors over just using that other thing instead?

Rultor does not support this feature. I don't know any other tools that do the same.

* Also for feature RFC's: How does this feature you're proposing complement the software development lifecycle of real teams? Compare what you're implementing to things that they're doing by hand, or using other tools. Please make sure that this feature is likely to be used by more than one project.

Other users have requested this feature. 

* For process RFC's: What do other major open-source projects do? Focus on ones that make their environment welcome to marginalized groups. If they have a reputation for being mean, we don't want to copy them.

NA

* What lessons can we learn from what other communities have done here? Why do they do what they do? Why is it applicable to bors, or why isn't it?

NA

* Papers: Are there any published papers or great posts that discuss this? If you have some relevant papers to refer to, this can serve as a more detailed theoretical background.

NA

# Unresolved questions

* What parts of the design do you expect to resolve through the RFC process before this gets accepted?

Specify and commit to a grammar and naming of the config property

* What parts of the design do you expect to resolve through the implementation of this feature before deployment?

NA

* What related issues do you consider out of scope for this RFC that could be addressed in the future independently of the solution that comes out of this RFC?

Commit body customisation
Additional reserved keywords for PR summary and description

# Future possibilities

Commit body customisation
Additional reserved keywords for PR summary and description

# See also

If this RFC already has a draft Pull Request, link to it here:

* Implemented by https://github.com/bors-ng/bors-ng/pull/1040

