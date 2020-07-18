Summary: Easily understand where in the implementation process an accepted RFC is.

<!-- RFC documents are put under a dual license, because the default license for content on this forum is CC-BY-NC-SA, while the license for bors's code is Apache 2.0 -->

> I allow this RFC document to be modified and redistributed under the terms of the Apache-2.0 license, or the CC-BY-NC-SA 3.0 license, at your option.

# Motivation

Once a RFC has been accepted, it is generally very common to show a status in some form whether it has been implemented or not. While the [archive][] shows all the accepted RFCs, it does not show what implementation status each RFC is in.

# Guide-level explanation

Every RFC in the archive will have a section at the end titled **Implementation Status** whose text will be one of the following:

* **Yet to be Implemented** - with a link to a github issue calling for it's implementation.
* **In Review** - with a link to github pull request which implements it.
* **Implemented** - with a link to the merged github pull request which implemented it and the date.

In addition, we will have a top-level page on the archive called *Implementation Status Summary* on which will have a summary of all the implementation statuses of the RFCs.

# Drawbacks

The maintainer might feel a bit tedious in managing the statuses of the RFCs. But the status change will happen only once or twice per RFC.

# Rationale and alternatives

Instead of doing both a Summary and an Implementation Status on each RFC, we can choose to do only one of them.

Instead of Implementation Status, we can just create an issue calling for the RFC implementation and link it to the RFC leaving the reader to following it properly using Github.

# Prior art

[IETF RFC 7492](https://tools.ietf.org/html/rfc7942)

# Future possibilities

Automate the RFC process. Once an RFC is accepted, an issue is created automatically and once that is fixed, RFC's implementation Status is updated.

[archive]: https://bors.tech/rfcs
