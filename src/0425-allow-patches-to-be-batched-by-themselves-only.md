Summary: Allow patches to be batched by themselves only.

<!-- RFC documents are put under a dual license, because the default license for content on this forum is CC-BY-NC-SA, while the license for bors's code is Apache 2.0 -->

> I allow this RFC document to be modified and redistributed under the terms of the [Apache-2.0 license](http://www.apache.org/licenses/LICENSE-2.0), or the [CC-BY-NC-SA 3.0 license](http://creativecommons.org/licenses/by-nc-sa/3.0/deed.en_US), at your option.

# Motivation

This RFC is to allow users to configure batch size limit by providing an option to allow patches to be batched by themselves only.

# Guide-level explanation

Appending "single on"or "single=on"  to the bors review command will force a patch to be batched by itself only and will never be put it a batch with other patches nor will other patches join its batch, like so:

> bors r+ single on
bors r+ single=on

Once a patch is reviewed with "single on" appended, all future reviews on the patch will remember the option and the patch will always be batched alone.

If this behavior is no longer desired, it can be disabled by appending "single off" or "single=off" to the review command and can be re-enable later by appending "single on" to a review command.

> bors r+ single off
bors r+ single=off

Additionally, the single command may be used without a review command like to turn the behavior on or off without having to review.

> bors single=on
bors single=off

# Reference-level explanation

The following options to the existing "bors r+" command, along with its alias, will be added:

> bors r+ single on
bors r+ single off
bors merge single on
bors merge single off
bors single=on
bors single=off

These commands will enable and disable the functionality to force a patch to always be batched by itself. Because the option is appended to the review command, once the option is handled, the review command will also be handled.

To support the option, the "patches" table will require a column to store the flag which will be checked before batching the patch. 

Checking whether a patch should be batched alone will be performed during the handling of the review command and during the bisection of a batch. Batches that are currently running or waiting will be left alone.

# Drawbacks

There shouldn't be any drawbacks since the option only affects patches which have received the specified command and can be disabled if desired.

# Rationale and alternatives

* This design is a simple solution to providing the users a method to limit batch size.
* An alternative solution would be allow the user to specify the max number of patches a batch can hold which would affect all patches in a project.

# Prior art

* The original bors system used implemented this solution. Unsure if other CI/CD systems implemented this system.

# Unresolved questions

* Is it necessary for the bisecting logic to honor the "single on" option? If the patch is already in a batch, it would either be merged in without any issues or fail sooner or later. Additionally, since the option is appended to the review command, the patch shouldn't ever be seen by the bisecting logic unless the patched r+'ed again while it's already in a batch.
* Should the option also handle patches that are already in a running/waiting batch? Should it pull out the patch and place it in a separate batch by itself?

# Future possibilities

None foreseen.

# See also

Implemented by pull request #839 on GitHub

# Implementation status

**Implemented**:

* Pull request: https://github.com/bors-ng/bors-ng/pull/839

