Summary: Add a new configuration option `status_wait_success`. Bors will wait until all of the GitHub Statuses listed in `status_wait_success` succeed (i.e. "turn green"), or until Bors times out.

> I allow this RFC document to be modified and redistributed under the terms of the [Apache-2.0 license](http://www.apache.org/licenses/LICENSE-2.0), or the [CC-BY-NC-SA 3.0 license](http://creativecommons.org/licenses/by-nc-sa/3.0/deed.en_US), at your option.

# Motivation

There are some GitHub Statuses that will initially report a failing (i.e. "red X") status, but at a later point in time will change their status to passing (i.e. "green checkmark"). An example would be the `codecov/project` status from CodeCov. If you have multiple CI jobs that each report partial coverage, then initially the `codecov/project` status will be failing. However, after all of the CI jobs have completed and submitted their coverage reports, CodeCov will merge all of the coverage reports and will change the `codecov/project` status to passing.

If you list such a status in the `status` section of your `bors.toml` file, then Bors will fail the build as soon as it sees a failing status. But this is not what we want - we want Bors to ignore those failing statuses and instead wait until the status passes (or until Bors times out, whichever comes first). 

A more detailed description of the CodeCov use case is available in [this forum thread](https://forum.bors.tech/t/bug-report-travis-should-not-look-at-the-codecov-status-if-the-travis-status-check-is-still-pending/356).

# Guide-level explanation

If you want Bors to require that a certain GitHub Status pass, and if you want Bors to immediately fail the build if that GitHub Status ever reports a failing (i.e. "red X") status, put that status in the `status` list.

If you want Bors to require that a certain GitHub Status pass, and you want Bors to wait until that GitHub status passes (i.e. "green checkmark"), and you do not want Bors to fail the build if that GitHub Status ever reports a failing status, put that status in the `status_wait_success`.

If you specify one or more statuses in the `status_wait_success` list, the Bors will wait until all of those statuses succeed. If not all of the statuses have succeeded by the time that the Bors timeout period has elapsed, then Bors will fail with a timeout error.

## Example 
For example, suppose that you want Bors to require that the following four statuses pass in order for Bors to merge:
- `continuous-integration/travis-ci/push`
- `Taskcluster (push)`
- `codecov/project`
- `codecov/patch`

If the `continuous-integration/travis-ci/push` and/or `Taskcluster (push)` statuses fails, you want Bors to immediately fail the build. However, if the `codecov/project` and/or `codecov/patch` statuses fails, you want Bors to ignore the failure, and instead wait until both the `codecov/project` and `codecov/patch` statuses succeed. In this example, your `bors.toml` file would look like this:

```toml
status = [ "continuous-integration/travis-ci/push", "Taskcluster (push)" ]

status_wait_success = ["codecov/project", "codecov/patch"]
```

## Customizing the timeout

The default Bors timeout is one hour (i.e. 3600 seconds). You can specifying a longer or shorter timeout by setting the `timeout_sec` option in `bors.toml`. For example, to set the Bors timeout to twelve hours (i.e. 43200 seconds), you would set `timeout_sec = 43200`. So your `bors.toml` file would look like this:
```toml
status = [ "continuous-integration/travis-ci/push", "Taskcluster (push)" ]

status_wait_success = ["codecov/project", "codecov/patch"]

timeout_sec = 43200
```

# Reference-level explanation

Here is a rough overview of the changes that we need to make.

First, we will modify the code that lives in [`lib/github/github.ex`, starting at line 224](https://github.com/bors-ng/bors-ng/blob/4b7e2594ba2b4557c54e4f3d9980147240f6656e/lib/github/github.ex#L224). Currently, the code looks like this:

```elixir
@spec map_state_to_status(binary) :: tstatus
def map_state_to_status(state) do
  case state do
    "pending" -> :running
    "success" -> :ok
    "failure" -> :error
    "error" -> :error
  end
end

@spec map_check_to_status(binary) :: tstatus
def map_check_to_status(conclusion) do
  case conclusion do
    nil -> :running
    "success" -> :ok
    _ -> :error
  end
end

@spec map_status_to_state(tstatus) :: binary
def map_status_to_state(state) do
  case state do
    :running -> "pending"
    :ok -> "success"
    :error -> "failure"
  end
end
```

We will modify it to look like this:

```elixir
@spec map_state_to_status(binary) :: tstatus
def map_state_to_status(state) do
  case state do
    "pending" -> :running
    "success" -> :ok
    "failure" -> :error
    "error" -> :error
  end
end

@spec map_check_to_status(binary) :: tstatus
def map_check_to_status(conclusion) do
  case conclusion do
    nil -> :running
    "success" -> :ok
    _ -> :error
  end
end

@spec map_status_to_state(tstatus) :: binary
def map_status_to_state(state) do
  case state do
    :running -> "pending"
    :ok -> "success"
    :error -> "failure"
  end
end

@spec map_state_to_status_wait_success(binary) :: tstatus
def map_state_to_status_wait_success(state) do
  case state do
    "pending" -> :running
    "success" -> :ok
    "failure" -> :running
    "error" -> :running
  end
end

@spec map_check_to_status_wait_success(binary) :: tstatus
def map_check_to_status_wait_success(conclusion) do
  case conclusion do
    nil -> :running
    "success" -> :ok
    _ -> :running
  end
end

@spec map_status_wait_success_to_state(tstatus) :: binary
def map_status_wait_success_to_state(state) do
  case state do
    :running -> "pending"
    :ok -> "success"
    :error -> "pending"
  end
end
```

Basically, we want Bors to think of any status other than `success` as `pending`.

Then we will add logic that checks the `bors.toml` file and calls the correct functions (i.e. `map_state_to_status` versus `map_state_to_status_wait_success`) depending on whether the relevant GitHub Status was listed in `status` or `status_wait_success`.

We should also make it a `bors.toml` parsing error to specify the same status in both `status` and `status_wait_success`.

# Drawbacks, rationale and alternatives

The drawbacks, rationale and alternatives have been explained in detail in [this forum thread](https://forum.bors.tech/t/bug-report-travis-should-not-look-at-the-codecov-status-if-the-travis-status-check-is-still-pending/356).

The feature described here is the only approach that avoids a race condition.

# Prior art

* I am not aware of any prior art.

# Unresolved questions

* How will we implement tests for this new feature?

# Future possibilities

I can't think of any future possibilities at this time.

# Implementation status

**Yet to implement**

