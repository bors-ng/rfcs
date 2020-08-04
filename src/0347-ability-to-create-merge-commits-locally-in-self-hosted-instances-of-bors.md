Summary: Add support for creating merge commits locally in self-hosted instances of Bors. This feature will never be enabled on the free public instance of Bors.

> I allow this RFC document to be modified and redistributed under the terms of the [Apache-2.0 license](http://www.apache.org/licenses/LICENSE-2.0), or the [CC-BY-NC-SA 3.0 license](http://creativecommons.org/licenses/by-nc-sa/3.0/deed.en_US), at your option.

# Motivation

Currently, Bors uses GitHub's [Git Database API](https://developer.github.com/v3/git/) to create merge commits. This approach results in relatively inexpensive disk storage requirements and is sufficient for most purposes.

However, there are several important features that are not possible when creating merge commits with the Git Database API. For example:

1. Merge commits created using the Git Database API are not automatically GPG-signed ([issue #647](https://github.com/bors-ng/bors-ng/issues/647)).

2. Merges performed using the Git Database API do not respect the merge strategies specified in any `.gitattributes` files ([issue #512](https://github.com/bors-ng/bors-ng/issues/512)).


# Guide-level explanation

An instance of Bors can be configured either to perform all merges using the Git Database API or to perform all merges locally using local `git`. This is a server-wide setting; the same setting will apply to all repositories on the Bors server. The default setting is to perform all merges using the Git Database API.

If you manage an instance of Bors, you can enable local `git` merges by setting the `perform_git_merges_locally` configuration setting to `true` in the [`config.exs` file](https://github.com/bors-ng/bors-ng/blob/a4c2f27367027172aad4c7624316025c6e6640aa/config/config.exs). The default value of `perform_git_merges_locally` is `false`.
```elixir
# General application configuration
config :bors, BorsNG,
  command_trigger: {:system, :string, "COMMAND_TRIGGER", "bors"},
  home_url: "https://bors.tech/",
  allow_private_repos: {:system, :boolean, "ALLOW_PRIVATE_REPOS", false},
  perform_git_merges_locally: {:system, :boolean, "PERFORM_GIT_MERGES_LOCALLY", true},
  dashboard_header_html: {:system, :string, "DASHBOARD_HEADER_HTML", """
          <a class=header-link href="https://bors.tech">Home</a>
          <a class=header-link href="https://forum.bors.tech">Forum</a>
          <a class=header-link href="https://bors.tech/documentation/getting-started/">Docs</a>
          <b class=header-link>Dashboard</b>
    """},
  dashboard_footer_html: {:system, :string, "DASHBOARD_FOOTER_HTML", """
        This service is provided for free on a best-effort basis.
    """}
```

By default, Bors will look for an executable named `git` in the PATH. To customize the location of the `git` executable, set the value of the `git_command` configuration setting. The default value of `git_command` is `git`.
```elixir
# General application configuration
config :bors, BorsNG,
  command_trigger: {:system, :string, "COMMAND_TRIGGER", "bors"},
  home_url: "https://bors.tech/",
  allow_private_repos: {:system, :boolean, "ALLOW_PRIVATE_REPOS", false},
  perform_git_merges_locally: {:system, :boolean, "PERFORM_GIT_MERGES_LOCALLY", true},
  git_command: {:system, :string, "GIT_COMMAND", "/bin/git"},
  dashboard_header_html: {:system, :string, "DASHBOARD_HEADER_HTML", """
          <a class=header-link href="https://bors.tech">Home</a>
          <a class=header-link href="https://forum.bors.tech">Forum</a>
          <a class=header-link href="https://bors.tech/documentation/getting-started/">Docs</a>
          <b class=header-link>Dashboard</b>
    """},
  dashboard_footer_html: {:system, :string, "DASHBOARD_FOOTER_HTML", """
        This service is provided for free on a best-effort basis.
    """}
```

This RFC does not deprecate or break any existing features. Maintainers of Bors servers will always have the option to perform all merges using the Git Database API.

# Reference-level explanation

If you were to perform the merge yourself, here are the steps you would follow. Suppose that we have a repository located at `https://github.com/someusername/somerepository.git`, and we want to merge pull requests `#10`, `#15`, and `#20` into the target branch `sometargetbranch`.

1. `git clone https://x-access-token:<token>@github.com/someusername/somerepository.git`
2. `cd somerepository`
3. `git checkout sometargetbranch`
4. `git fetch origin pull/10/head:randomlygeneratedstring-10`
5. `git checkout randomlygeneratedstring-10`
6. `git fetch origin pull/10/head:randomlygeneratedstring-15`
7. `git checkout randomlygeneratedstring-15`
8. `git fetch origin pull/10/head:randomlygeneratedstring-20`
9. `git checkout randomlygeneratedstring-20`
10. `git checkout sometargetbranch`
11. `git branch --force staging`
12. `git checkout staging`
13. `git merge randomlygeneratedstring-10 randomlygeneratedstring-15 randomlygeneratedstring-20`
14. `git push --force origin staging`
15. `cd ..`
16. `rm -rf somerepository`

In order to implement this in Bors, we'll likely need to write additional versions of the `start_attempt` method in [attemptor.ex](https://github.com/bors-ng/bors-ng/blob/a4c2f27367027172aad4c7624316025c6e6640aa/lib/worker/attemptor.ex) and the `start_waiting_batch` and `start_waiting_merged_batch`  methods in [batcher.ex](https://github.com/bors-ng/bors-ng/blob/a4c2f27367027172aad4c7624316025c6e6640aa/lib/worker/batcher.ex). Then, we'll add logic that calls the appropriate methods based on the value of the `perform_git_merges_locally` configuration option.

# Drawbacks

In order for Bors to perform a merge locally, the server on which Bors is running will need to have sufficient disk space for `git` to be able to clone repositories locally. Thus, any Bors server maintainer that is considering setting `perform_git_merges_locally` to `true` should make sure that they will have enough disk space.

Let `N` denote the number of repositories on which Bors is installed, and let `M` denote the size on disk of the largest repository. Since each repository could have both `bors r+` and `bors try` commands running simultaneously, a conservative estimate for the disk space requirement for cloning repositories is `2MN`.

# Rationale and alternatives

The alternative will be to continue using the Git Database API for creating merge commits. This is a great option for most users, and it will continue to be the only option available on the free public instance of Bors. However, as described above, there are certain features that will never be available through the Git Database API, and for those, a Bors administrator will need to use local merges.

# Prior art
The following apps implement the NRSROSE and have the option to create the merge commit locally:
* [homu](https://github.com/servo/homu) ([source file](https://github.com/servo/homu/blob/2ea53e76ebac3e5fa11bc39054b3cd4c42eff607/homu/main.py#L693))
* [zuul](https://github.com/openstack-infra/zuul) ([source file](https://github.com/openstack-infra/zuul/blob/dc9347c1223e3c7eb0399889d03c5de9e854a836/zuul/merger/merger.py#L382))
* [marge-bot](https://github.com/smarkets/marge-bot) ([source file](https://github.com/smarkets/marge-bot/blob/4813da4baf9e92f8d429990a2fccf03baea1b668/marge/git.py#L104))

# Unresolved questions

* How do we implement tests for this feature?

# Future possibilities

### Next steps for maintainers of Bors servers after this feature is implemented

##### GPG-signing all merge commits

If you run a Bors server and you want to enable GPG-signing of all merge commits, you first need to set `perform_git_merges_locally` to `true`. Then, you should log in to the server as the same user that Bors runs as and run the following commands:
* `git config --global user.signingkey your_gpg_key_id`
* `git config --global commit.gpgsign true`

##### Using specific merge strategies for certain files

If you use a Bors server with `perform_git_merges_locally` set to `true`, and you want to use a specific merge strategy for certain files in one of your repositories, you only need to create an appropriate `.gitattributes` file and commit it in your repository. The command-line `git` will use the `.gitattributes` file automatically.

### Future work in Bors

Once the ability to create merge commits locally has been implemented, future work might include the implementation of a `git_squash = true` option in `bors.toml` that would pass the `--squash` flag to the `git merge` command. This could potentially help us close [issue #138](https://github.com/bors-ng/bors-ng/issues/138) and/or [issue #194](https://github.com/bors-ng/bors-ng/issues/194).

But this would be an advanced feature, and we should not implement it as part of this first RFC. The best plan is to first implement the ability simply to create merge commits locally. Once we have implemented that, we can consider opening a second RFC for discussion specifically of a `git_squash = true` option for passing the `--squash` flag to the `git merge` command, or something similar.

# Implementation status

**Yet to be implemented**
