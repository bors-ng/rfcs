Summary: This RFC proposes to expand, and make more explicit, decision-making for bors-ng. It puts in place a template for new proposals (with typical sections such as Motivation, Summary, Drawbacks), a plan for accepting and rejecting them, and sets the stage for better governance changes in the future (it doesn't set up a core team, but once the core team is put in place, it'll follow the RFC process). This "initial RFC" shall be approved following its own process; nobody seemed to have a problem with it during the spitballing stage.

> I allow this RFC document to be modified and redistributed under the terms of the [Apache-2.0 license](http://www.apache.org/licenses/LICENSE-2.0), or the [CC-BY-NC-SA 3.0 license](http://creativecommons.org/licenses/by-nc-sa/3.0/deed.en_US), at your option.

# Motivation

Some of you might remember [way back when](https://github.com/bors-ng/bors-ng/pull/232) @khodzha opened a pull request for an E-easy issue, but @Macarse, who had never even realized the issue existed, wasn’t sure if it was a good idea or not. I (@notriddle) overruled their concerns, mostly because I figured it would be kind of mean to have a newbie open a pull request just to have it rejected with “actually, we didn’t want that anyway”. This essentially indicates a mismatch of expectations: @khodzha and @notriddle thought that the decision had already been made, while @Macarse still thought it was up for debate.

This is not the only time I ended up making judgement without very much discussion in response to an issue report, pull request, or support thread. Even when there is feedback, it isn’t very organized, and the design decisions that are made there get mixed with support questions, bug reports, and announcements. We need a statement of record about what has been decided and who decided it, and it needs to strike the reasonable balance between “set in stone” and “overridden on the Benevolent Dictator Pro Tempore’s daily whims”. It also needs to maintain the balance between “maintaining a coherent features set and overall vision” and “getting community involvement in decision making”.

# Guide-level explanation

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

Once you've made sure it's appropriate for an RFC, open a new topic in the #development:draft-rfcs section of the forum. Fill out the included template, tag it as feature, process, or informational, and then respond to any feedback.

RFCs rarely go through the process unchanged. The goal is to refine your proposal so that it can answer most sensible objections on its own, without having to reread the entire discussion thread. "Out-of-band" discussions, either in forum private messages, company chatrooms, or even in-person meetings, are common; just make sure you post a summary on the in-band discussion thread, and that anything truly important gets included in the RFC text itself. As the discussion goes along, try to keep the RFC text up-to-date with whatever consensus seems to emerge.

At some point, a member of the project leadership will place the RFC into Final Comment Period, the #development:fcp-rfcs category. After two final weeks of remaining open, your RFC will finally be accepted or rejected. The FCP is usually pretty quiet, since it's only really opened after a consensus has already emerged. Once it's approved, your RFC will be numbered and archived.

## After an RFC has been accepted

Once an RFC becomes "approved," authors may implement it and submit the feature as a pull request to the bors-ng repo. Being approved is not a rubber stamp, and in particular still does not mean the feature will ultimately be merged; it does mean that in principle all the major stakeholders have agreed to the feature and are amenable to merging it.

Furthermore, the fact that a given RFC has been accepted implies nothing about what priority is assigned to its implementation, nor does it imply anything about whether a developer has been assigned the task of implementing the feature. While it is not *necessary* that the author of the RFC also write the implementation, it is by far the most effective way to see an RFC through to completion: authors should not expect that other project developers will take on responsibility for implementing their accepted feature.

If an accepted RFC needs to be modified, please flag it for a moderator, using the "Something Else" option and mentioning what needs changed. Most of the time, an RFC won't need changed after it's been accepted, but the nature of this process means that we cannot expect every accepted RFC to actually reflect what the end result will be once the change has actually been tested out. Only very minor changes should be submitted as amendments. More substantial changes should be new RFCs, with a note added to the original RFC.

# Reference-level explanation

## The project leadership

Throughout this RFC, the project leadership will be a role given a number of responsibilities. In particular, project leadership approves and rejects RFC’s.

Currently, the project leadership is Michael Howell, also known as @notriddle, the Benevolent Dictator Pro Tempore. Future RFC’s are expected to replace him with a team structure, who will act in consensus as the project leadership.

## The RFC process

There are four RFC categories, each of which corresponds to a stage:

* #development:draft-rfcs : This is the first actual stage in the process. The responsibilities of all participants are spelled out below this list.
  * #development:fcp-rfcs : The last part of the discussion stage, where the discussion is timeboxed at two weeks. An RFC will always go through this phase before being accepted, and will likely, though not necessarily, go through this phase before being rejected.
* #development:approved-rfcs : An RFC that has been accepted for implementation.
* #development:rejected-rfcs : An RFC that has not been accepted, and likely will not be accepted.
* #development:postponed-rfcs : An RFC in this category will likely be moved back to draft later. This phase is used when no process seems to be made, yet the leadership doesn't want to accept or reject it yet either, such as if the discussion is blocked on something external to the project.

### Responsibilities of the author

The individual (or, I guess, an organization would be allowed to act as an author as long as they can agree on everything out-of-band before acting together as a "hive mind") who opens an RFC up will act as the RFC’s “author”. They are responsible for defending or editing the RFC based on criticism that comes up during the discussion, acting as the RFC’s advocate.

If the original author no longer wishes to champion the RFC, they can hand it over to someone else (obviously, the new author and the old author both have to publicly agree to this) or withdraw the RFC. If the RFC is withdrawn, it will be closed.

For any discussions that happened before the RFC was opened that the author thinks are relevant, the author should copy any knowledge they created into the RFC or the discussion thread. They should also copy any necessary knowledge from the RFC’s own discussion thread into the RFC, so that the RFC’s text can stand alone. Linking to external discussions is allowed, but they should copy or summarize it in case the third-party discussion host goes away.

### Responsibilities of the project leadership

While anyone can participate in the RFC discussion, the final decision about approving or rejecting an RFC lies with the project leadership (and the mod team, if they decide that an “RFC” is pure spam they can just delete it, but the mod team can’t just accept an RFC). They do not have to put it up to a community vote, though they will need to come to an internal consensus, and they may ask for a non-binding poll if they think it would help.

The project leadership is expected to voice their concerns, allowing the author to edit or defend the RFC. As described in the Code of Conduct, the project leadership shall make every effort to maintain a professional atmosphere, and to make the author feel that they are being helped, not hindered. The win condition is one where everyone involved feels that they made the best possible decision with what they have.

Project leadership must put an RFC into a “Final Comment Period” if they wish to accept it, and may (but need not) do so if they wish to reject it. Final Comment Period lasts two weeks (14 days), to make sure that anyone who receives RFC’s in weekly digests has a chance to respond. After FCP, all non-staff commenting is disabled and project leadership must make their final decision, moving the RFC from the discussion phase on to the accepted or rejected category.

The RFC document must also be de-wiki-fied after the RFC is closed, and if the RFC is accepted, it must be assigned a number, archived as a GitHub Page, and have a tracking issue created.

### Responsibilities for all discussion participants

The most important rule is to keep tabs on the discussion, and avoid relitigating a point that has already been made. As always, follow the [Code of Conduct](https://bors.tech/documentation/code-of-conduct/).

It is highly recommended that third-party participants avoid acting as the devil’s advocate. Instead, please represent what your actually needs are in an honest way; the personal experience of individuals who are not the champion or a member of the project is the most valuable contribution that third-party participants can make.

Also, participants should send any mechanical corrections (spelling, grammar, formatting) to the author as a Private Message, not as a public post in the thread. The project leadership appreciates mechanical fixes, and the author probably does too, but once they’ve been fixed any post that corrects them is just clutter, not something that people who want to understand what happened should have to read through years later. Alternatively, if the participant has a high enough permission level on the forum, they may simply perform mechanical fixes to the RFC themselves.

Meta-discussion, surrounding the quality of the discussion itself rather than the RFC text directly, should be kept in a separate #meta thread or in a Private Message. And while the ethical implications of an RFC are perfectly welcome in the in-band discussion thread, problems that are related to the background or behavior of participants should, again, be kept to the #meta section, to PMs, or should simply be handled by flagging the offending post and moving on.

# Drawbacks

So far, this RFC basically copies what [Rust does](https://github.com/rust-lang/rfcs/blob/41406150ed48491281f952b04db6623dcdc4c90c/text/0002-rfc-process.md), with a couple of tweaks to fit Bors’s slower development pace and a few notes and bits pulled from [Swift Evolution](https://github.com/apple/swift-evolution/blob/6065aef88c7b45c9a588cd481450c5dadb943117/process.md), [BEP](http://bittorrent.org/beps/bep_0001.html), and [PEP](https://www.python.org/dev/peps/pep-0001/).

This means we’re already very familiar with the major drawbacks:

* Public RFC’s have been known to attract a lot of attention, sometimes creating a discussion that is so long that it’s impossible to read it all, which compounds the problem because it causes people to post the same concern twice.
* Public RFC’s also attract concern trolls, since they have so much attention paid to them, making the length problem even worse.
* Settling on a single canonical discussion medium requires one to make final decisions about a bunch of trade-offs, such as ease of archive (the contents of a git repository excel at this one), moderation tools (Discourse excels at this one), familiarity (GitHub Issues excel at this one), ease of entry (chat rooms excel at this one), sophisticated review features ([reviewable.io](http://reviewable.io) and Gerrit excel at this one), and others that I can’t think of right off the bat. The disorganized status quo allows participants to make an in-the-moment best call.
  * Since this version of the RFC RFC uses Discourse, it sacrifices ease of entry and archive in favor of Discourse's customizability and moderation tools.
* This RFC process is essentially waterfall; implementing the feature is expected to happen after the feature has been planned. While amending an RFC is expected to happen on occasion, it's heavy-weight enough, and this process is slow enough, that an implementer may choose to stick to the plan even if they know it's wrong to avoid having to propose a change of plan.
* It is also very easy to spend a lot of time arguing about one minor problem, only to find out later that there's something way more important to figure out later.

# Rationale and alternatives

In spite of all the ink spilled in this document, the described process is actually very lightweight. It can be summarized as: open a topic in the RFC subforum, argue about it in the comments, and it gets approved or rejected. Most of the RFC Process RFC is just trying to head off forms of dysfunction that have been seen in other places, which includes describing things in excruciating detail, or in some cases even spelling out parts of the process that don't exist.

* This process avoids a lot of features that Python PEPs have, such as Editors. Adding a role like that might help reduce the noise that project leadership is exposed to, but that hasn't really been a problem yet, and nobody likes having all their time spent dealing with gatekeeping nonsense.

* The other big alternative is to just continue on as-is. Such a choice seems livable, but not really good, for reasons spelled out in Motivation.

* This RFC introduces the RFC process first, and postpones creating a core team for later. The alternative would be to try to set up a core team first, *and then set up an RFC process*. The main reason for going with the RFC-process-first approach is that the RFC process is designed to deal with specific pain points, even under the BDPT model.

# Prior art

* [PEP 8002](https://www.python.org/dev/peps/pep-8002/) does a nice job of examining other project governance systems
* The process spelled out in this "RFC process for bors" is particularly modeled after [Rust's RFC-0002](https://github.com/rust-lang/rfcs/blob/41406150ed48491281f952b04db6623dcdc4c90c/text/0002-rfc-process.md), with additional references to [PEP 0001](https://www.python.org/dev/peps/pep-0001/).
* Some changes made here are modeled after stuff discussed in ["scaling empathy"](https://manishearth.github.io/blog/2019/02/04/rust-governance-scaling-empathy/) and ["organizational debt"](https://boats.gitlab.io/blog/post/rust-2019/). While this RFC certainly does not attempt to plan a process that will scale like Rust needs (we just aren't that big yet), the process should be adaptable enough, and some tweaks were deliberately made so that Bors can experiment with things that were discussed for use in Rust.
* Part of the moderation policy in general is designed to avoid [this kind of failure mode](https://lobste.rs/s/v9rktg/new_users_ethics_civility_threads_you).
  * Note that Lobsters and Discourse have very different treatments of flagging: in Discourse, if three users with TL1 or better flag a post, the post will be hidden regardless of how many likes it gets. This was an intentional driving force in choosing Discourse instead of, for example, Reddit; I are intentionally taking an "if there's reasonable doubt on whether it's okay, hide it until a moderator comes around" approach.
  * The bors approach also specifically avoids trying to take a "self-governing" moderation approach in favor of just biting the bullet and using a hierarchy.
  * And, of course, because topics are not sorted by likes, it seeks to [encourage discussion](https://meta.discourse.org/t/opinion-kids-would-you-please-start-fighting/73450) rather than groupthink.
* Basically, [bors is at the first stage](https://communityroundtable.com/what-we-do/research/community-maturity-model/).

# Unresolved questions

* Is this too much?
* An RFC only gets a number when it's approved, right?
* Should I bother archiving the RFCs in GitHub pages or Amazon S3 or something? It seems like a good idea to have as many backups as possible for these things, but I'm not sure if it's truly necessary.

# Future possibilities

## The Core Team and The Moderation Team

This is the biggie; instead of just having notriddle check everything, there should probably be a small group who decides whether to approve an RFC or not.

Once the core team exists, it'll probably also be worthwhile to have an automated "proposal for FCP" system like Rust's rfcbot.

## RFC automation

In the current implementation, a lot of stuff is done manually upon approving an RFC:

* Giving RFCs numbers
* Archiving them on GitHub Pages
* Creating tracking issues
* Un-wiki-fying the initial post

All of these steps can be automated, but it should probably all be done after some experience is had with the process; we don't want to set stuff in stone too early.

## RFC tracking

Similarly, it would be really cool to have an automatically-built Kanban board. Perhaps just a GitHub Project generated using the API.

<table>
<thead><tr><th>Discussion</th><th>Final Comment Period</th><th>Approved</th><th>Implemented</th><th>Rejected/Withdrawn</th><th>Postponed</th></tr></thead>
<tbody><tr>
<td>

Write Status to a GitHub Check

Minimally-Viable GitLab Support

New-Style Emoji

Non-Batching Mode

</td><td>

Batch-Size Limit

</td><td>

Localization Support for the Web Site

GitLab Worker Protocol

Basic REST API

</td><td>

Try Cancellation Command (`bors try-`)

</td><td>

Vape spam

Turn bors into a SPA

Full-blown issue tracking

</td>

<td></td>

</tr></tbody></table>