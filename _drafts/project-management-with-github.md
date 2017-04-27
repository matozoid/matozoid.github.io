---
layout: post
title:  Project management with Github
date:   2017-11-17 16:16:01 +0100
comments: true
tags: javaparser
---
Well, I've been the maintainer of a popular project on github for a few years now,
and lately we've been having weekly releases.
I'll write down how the flow works:

### Handling an incoming issue
When an issue comes in, I try to figure out what it is:
- is it a bug report? Tag it as a "Bug report".
- is it any other change? Tag it as a "Change request". (This is currently not done this way, but I'll fix it some day)
- is it a question? Tag it as "Question". Try to answer it right away.
- is it a nice idea, but it shouldn't be part of the project? Tag it "Fun project idea".
People are free to start a project like this and they'll get support from the team,
but it is not a goal, nor will it become part of the project.
- is it something else? Sorry, but there is nothing else :-)

Bug reports make me nervous, change requests are just interesting, questions are fun.

Now for more labels:
- "Easy" if someone could fix it without knowing much about JavaParser.
In general our most valuable contributions are from people who use JavaParser for their project,
not from people who come in looking for something to do.
Since we like giving people the opportunity to learn, even if their contribution is not big,
we have this tag for them to find work.
- "Need more input" is for issues where we can't figure out what to do,
so we need more input from other people.
In practice this input seldom arrives, making this label a little useless.

Finally there are all kinds of convenience labels.
We can ask "how many people are complaining about the comment support?" or
"are we finished implementing Java 9 support?" this way.

Try to find some time to write a reply to the issue - we had a time when issues stayed open for months.
When we finally got to respond to them, the reporter was no longer interested most of the time.

### Handling a pull request

### Making a release

- all the labels
- next_release and next major release milestones
- mvn release & bintray
- BulkParseTest
