# Bors RFCs

The [Draft RFCs] are over there. This is just an archive of accepted RFCs.

## What's an RFC?

Major changes and events are usually proposed by opening an RFC. What counts as "major" can get subjective, but any of these will count:

* Feature RFC’s, which include:
  * All breaking changes, such as removing or renaming commands, bors.toml configuration options, environment configuration options, changing the name or packaging of the OTP application (which would break people’s deployment scripts), or bumping the Erlang or Elixir version requirement (we will probably make an RFC putting breaking dependency bumps onto some kind of schedule)
   * Adding new features with config options and commands, because they need to be tested and maintained to avoid breakage when users rely on them
* Informational RFC’s, which include:
   * Starting a talk, meetup, or social networking account that will be expected to officially “represent bors-ng”
   * Documenting design issues, deciding to never implement a feature, proposing an experiment, or recording a proof-generated insight
* Process RFC’s, which include:
   * Changing the RFC process, the organization of the issue tracker or the support forum, or other community infrastructure
   * Amending the Code of Conduct

Major changes *do not* include:

* Most moderation actions
* Bumping a hex.pm dependency, since the Distillery release process automatically pulls those in anyway
* Refactoring the codebase
* Making a blog post
* Fixing something that is clearly a bug, such as breaking the Not Rocket Science Rule, crashing, or displaying wrong information
* Minor look and feel improvements, or even minor feature additions to the dashboard that aren’t likely to translate to a we-will-not-break-this commitment

If you submit a pull request to implement a new feature without going through the RFC process, it may be closed with a polite request to submit an RFC first.

## How to submit an RFC

Once you've made sure it's appropriate for an RFC, open a new topic in the [Draft RFCs] section of the forum. Fill out the included template, tag it as feature, process, or informational, and then respond to any feedback.

[Draft RFCs]: https://forum.bors.tech/c/development/draft-rfcs

RFCs rarely go through the process unchanged. The goal is to refine your proposal so that it can answer most sensible objections on its own, without having to reread the entire discussion thread. "Out-of-band" discussions, either in forum private messages, company chatrooms, or even in-person meetings, are common; just make sure you post a summary on the in-band discussion thread, and that anything truly important gets included in the RFC text itself. As the discussion goes along, try to keep the RFC text up-to-date with whatever consensus seems to emerge.

At some point, a member of the project leadership will place the RFC into Final Comment Period, the [FCP RFCs] category. After two final weeks of remaining open, your RFC will finally be accepted or rejected. The FCP is usually pretty quiet, since it's only really opened after a consensus has already emerged. Once it's approved, your RFC will be numbered and archived.

[FCP RFCs]: https://forum.bors.tech/c/development/fcp-rfcs

## After an RFC has been accepted

Once an RFC becomes "approved," authors may implement it and submit the feature as a pull request to the bors-ng repo. Being approved is not a rubber stamp, and in particular still does not mean the feature will ultimately be merged; it does mean that in principle all the major stakeholders have agreed to the feature and are amenable to merging it.

Furthermore, the fact that a given RFC has been accepted implies nothing about what priority is assigned to its implementation, nor does it imply anything about whether a developer has been assigned the task of implementing the feature. While it is not *necessary* that the author of the RFC also write the implementation, it is by far the most effective way to see an RFC through to completion: authors should not expect that other project developers will take on responsibility for implementing their accepted feature.

If an accepted RFC needs to be modified, please flag it for a moderator, using the "Something Else" option and mentioning what needs changed. Most of the time, an RFC won't need changed after it's been accepted, but the nature of this process means that we cannot expect every accepted RFC to actually reflect what the end result will be once the change has actually been tested out. Only very minor changes should be submitted as amendments. More substantial changes should be new RFCs, with a note added to the original RFC.
