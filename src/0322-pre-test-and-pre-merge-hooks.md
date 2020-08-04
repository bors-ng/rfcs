Summary: Provide a way for projects to run arbitrary code before testing, and before merging.

> I allow this RFC document to be modified and redistributed under the terms of the [Apache-2.0 license](http://www.apache.org/licenses/LICENSE-2.0), or the [CC-BY-NC-SA 3.0 license](http://creativecommons.org/licenses/by-nc-sa/3.0/deed.en_US), at your option.

# Motivation

Consider an auto-formatter, like clang-format. When should you run it?

- You could assume everyone contributing to your project will run it before committing. But this is error-prone and requires changes to many individual users' computers, which isn't practical, especially for open-source projects.
- You could use your CI to enforce that the linter has been run: run it on a temporary checkout, see if it makes any changes, and if it does then fail the test and force someone to fix it by hand. But creates extra work for contributors.
- You could make a bot that automatically formats PRs, and pushes the changes back into the PR branch. But inserting commits into a PR branch like this causes problems for users when they want to update their PR, because now the PR branch on github doesn't match their PR branch locally. This is annoying, and contributors with less experience with git may not be able to recover at all.

The ideal workflow, therefore, is: approve PR -> merge from master -> auto-format -> test -> merge to master. The "pre-test" hook in this RFC allows bors-ng to handle everything after the approval. And you probably also want to run this during "try" commands too, so we provide a "pre-try" hook as well.

Consider a continuous deployment workflow, where everytime you merge into master, you also want to upload the latest version somewhere. Since bors-ng takes care of merging into master, it seems like a natural place to handle this uploading step. This RFC defines a "pre-merge" hook to handle this.

There's some more discussion in this thread: https://forum.bors.tech/t/running-custom-code-before-and-after-the-merge-for-continuous-deployment-or-other-uses/315

# Guide-level explanation

In `bors.toml`, you can set *pre-test*, *pre-try*, and *pre-merge hooks* to be a list of URLs:

```toml
pre-test-hooks = ["https://example.com/pre-test-example"]
pre-try-hooks = ["https://example.com/pre-try-example"]
pre-merge-hooks = [
    "https://example.com/pre-merge-example1",
    "https://example.com/pre-merge-example2",
]
```

The pre-test and pre-try hooks are invoked after bors-ng has prepared a commit for testing, but before running tests. They may be used to alter the code being tested – for example, by running an auto-formatter. The pre-merge hook is invoked after an approved commit has passed testing, just before bors-ng merges it into the target branch. It cannot alter the code, but it can do other things, like trigger a deployment process or create a tag.

A hook is "invoked" by `POST`ing a JSON document to the given URL – something like:

```json
{
    "phase": "pre-test",
    "repository": "https://github.com/bors-ng/bors-ng",
    "work-branch": "staging.tmp",
    "target-branch": "master",
    "commit-id": "0b7d6e1ccf9d5fdbce73b690da8a4f76fffc38eb",
    "timeout": 60,
    "callback": "https://bors.tech/webhook/callback/5a4857627"
}
```

More keys may be added in the future. Unrecognized keys should be ignored.

When implementing a hook, the first thing you should do is validate the payload. Normally this includes the following steps:

1. Confirm that the request actually came from the bors-ng service, by checking the `X-Bors-NG-Signature` header. This will contain a comma-separated list of signatures, like:

   ```
   X-Bors-NG-Signature: sha256-hmac=5257a869e7ecebeda32affa62cdca3fa51cad7e77a0e56ff536d0ce8e108d8bd, some-future-algorithm=6ffbb59b2300aae63f272406069a9788598b792a944a07aba816edb039989a39
   ```

   Right now the only supported form is `sha256-hmac`, which will contain hex-encoded SHA256-HMAC of the request body. The key is a unique per-repo secret, which can be obtained from your bors-ng dashboard. In the future, more algorithms may be added, and you should be prepared to detect and ignore unrecognized signatures.

   Remember to use a constant-time equality test whenever checking HMAC signatures. For example, in Python use [`hmac.compare_digest`](https://docs.python.org/3/library/hmac.html#hmac.compare_digest).

2. Before manipulating the `work-branch`, confirm that its HEAD matches the given `commit-id`. This helps avoid race conditions in case the bors-ng service and your hook get confused and out-of-sync.

   Depending on what your hook does, it may also make sense to revalidate this commit id later. For example, if you're going to mutate the branch, don't use `git push --force`; always use `git push` or [`git push --force-with-lease`](https://blog.developer.atlassian.com/force-with-lease/).

The hook responds by `POST`ing JSON documents to the given `callback` URL. These documents look like:

```json
{
    "status": "success",
    "comment": "Hook complete - for details see [here](https://...)"
}
```

The `"status"` field must be one of `"success"`, `"failure"`, or `"pending"`. The `"comment"` field contains arbitrary Github-flavored Markdown.

The `"timeout"` field in the request body indicates how many seconds bors-ng is willing to wait for the callback to be invoked. If bors-ng receives a `"pending"` response, then it resets its timeout. We recommend that if a hook needs more than this amount of time to run, then it should send a `"pending"` callback every (timeout/2) seconds to extend the timeout.

# Reference-level explanation

## Hook lifecycle

Hooks are always invoked in sequence with each other and with other operations on the branch, never in parallel. For example, if there are two pre-test hooks, then bors-ng performs the following steps in order:

- Set up the `staging.tmp` branch
- `POST` to the first hook
- Wait for the first hook's callback to be invoked.
- `POST` to the second hook
- Wait for the second hook's callback to be invoked
- Rename `staging.tmp` → `staging` to start the test run.

Rationale: hooks are allowed to mutate things and may fail. Running them sequentially makes it much easier to reason about these cases.

If a hook returns a non-200 response, the timeout expires, the callback is invoked with any `status` besides `"success"` or `"pending"`, or the callback payload is invalid, then the run is immediately aborted, and an error is reported to the user, similar to a test failure. If the run is a rollup, then the entire rollup is failed; no attempt is made to bisect the failing commit. (Rationale: hooks are not tests; they're intended to always succeed. So if a hook fails, there's no point in trying to figure out which commit to blame; the hook is to blame, and the hook is what needs to be fixed.)

If a pre-merge hook alters the working branch (i.e., after running it, the head of the test branch has changed), then bors-ng should detect that and report an error rather than merge an untested commit.

## Request payload

The request payload is a JSON object with the following keys:

- `phase`: one of `"pre-test"`, `"pre-try"`, or `"pre-merge"`
- `repository`: the canonical URL for the repository
- `work-branch`: the branch in that repository that bors-ng is working on. Generally `"staging.tmp"` for pre-test hooks, `"trying.tmp"` for pre-try hooks, and `"staging"` for `pre-merge` hooks.
- `target-branch`: For pre-test and pre-merge hooks, the name of the target branch that this change will eventually be merged into. For `pre-try` hooks, this is always `null`.
- `commit-id`: Bors-ng believes that this commit is the HEAD of the `work-branch`.
- `timeout`: The number of seconds bors-ng will wait before assuming the hook has crashed.
- `callback`: The URL the hook should `POST` updates to.

## Security

We need some way for hooks to tell that they're being invoked by bors-ng, and for bors-ng to tell that the callbacks are coming from the hooks.

The latter is fairly easy: bors-ng should create a new, high-entropy callback URL for each hook invocation. That way it knows that anyone who invokes the callback is in fact authorized to do so.

For invoking hooks, life is more difficult, because we expect hook URLs to be checked into public repositories. And if hooks don't validate that it's really bors-ng invoking them, then anyone who knows the hook URL could send it a POST that pretends to be from bors-ng. If timed correctly, this might cause a hook to try to auto-format a `testing.tmp` branch that bors-ng is in the middle of assembling, or trigger the deployment of a branch whose tests are still running. That would be bad.

Therefore, each time bors-ng adds a new repo, it generates a 256-bit secret. This secret is shown on the repo's dashboard page, and can be manually regenerated by the user (e.g. by pressing a button on the dashboard).

When bors-ng invokes a hook, it includes a `X-Bors-NG-Signature` header, containing a SHA256 HMAC of the request body, keyed by this secret: `sha256-hmac=<256 bits of hex>`. (Prior art: [Github](https://developer.github.com/webhooks/securing/), [Stripe](https://stripe.com/docs/webhooks/signatures).)

The header should also contain some fake signatures with randomly generated names, like `sdof123-jow=<more gibberish here>`. This forces hook implementations to be prepared to handle unrecognized signature algorithms, so that if we later need to transition to a new signature algorithm we can do that without breaking everyone ([see also](https://tools.ietf.org/html/draft-ietf-tls-grease-02)).

# Drawbacks

It's kind of complicated? But auto-formatting and continuous deployment are pretty awesome things.

# Rationale and alternatives

We could provide a way for bors-ng to run arbitrary code directly. That might be acceptable for locally-hosted versions, or with complex sandboxing. But we would like to support this feature in the publically hosted version, and without complex sandboxing. This is the simplest approach I can think of that doesn't require trusting the hook code.

In practice, it seems likely that the pre-test and pre-try hooks will often be the same. I kept them separate because I'm not sure whether this is always true or not.

The pre-merge hook has a weaker rationale than the others, because there are other ways to invoke code after your tests pass, or after code is merged into master (e.g. the deployment support in most popular CI systems). However, it still seems to be worth including, because:

- In the continuous deployment use case, you probably want pre-test and pre-merge hooks to collaborate (e.g., setting the version number in pre-test, and then creating the corresponding tag in pre-merge). Splitting this between two different services seems like a pain.
- If you're using multiple status checks and want to gate deployment on all of them together, then that requires some separate service. bors-ng is already set up to do this.
- Most hosted CI systems expose deployment secrets to all users with write access to the repository. In bors-ng deployments, its common that there are users who have write permission (e.g. to create branches or modify issues), but who don't have permission to push directly to the master branch. Storing the deployment secrets in some other system, whose access is gated behind bors-ng, is a way to enforce the NRSROSE principle for deployments as well as merges.
- We expect that the cost of implementing pre-merge hooks should be pretty low, since they should be able to share most of their code with pre-test/pre-try hooks.

# Prior art

I'm not aware of any prior art for these kinds of hooks in other NRSROSE systems.

# Unresolved questions

None.

# Future possibilities

I briefly considered adding a mechanism to pass information between the pre-test and pre-merge hooks. (For example: the pre-test hook could return some JSON data, which would then be passed through to the pre-merge hook.) I couldn't think of any use cases, so I left it out, but it could be added later if any use cases are discovered.

If you have a lot of repositories managed by bors-ng, then it might be tiresome to have to manually configure secrets for each one. Maybe we'll want to add a way to let multiple repositories use the same secret? (E.g., a per-github-org secret instead of a per-repo secret.)

# Implementation status

**Yet to be implemented**
