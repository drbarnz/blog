+++
title = "Slack custom emoji shinighans"
description = "Having a "
date = 2022-02-28
updated = 2022-02-28

[taxonomies]
categories = ["tech"]
tags = ["slack", "security", "web"]
+++

This will be a quick one, I swear.

I'll start with the morale of the story:

> Some things are unexpected exhilf paths when they are working as designed.

This story is about a widely used and the "IRC-of-web2.js": Slack and its loved custom emojis.

## Custom emojis, how do they work

Let's all get one the same page here:

 * Slack lets you to just upload little `png`'s 
 * These are great for building a sense of community on a slack space
 * A lot of memes get uploaded

To dig deeper you need to use a tool that is considered illegal in some jurisdictions,
so use at your own risk: Inspect Element.

![A screenshot of Firefox development console with an image tag highlighed and a hover image of Picard faceplaming](/images/slack_custom_emoji/inspect-element.png)

## Slack emojis are *PUBLIC* and *UNAUTHENTICATED*

I'll be using a open invite Slack instance for these examples: [lastweekinaws.slack.com](https://lastweekinaws.slack.com/)
hosted by the [Duck Bill group](https://www.duckbillgroup.com/). That Slack's team id is: `T02N6U1DVD4`.

> Not a paid advertiment, just here to spread good shit posters *cough* [@quinnypig](https://twitter.com/quinnypig) *cough*.

So, as you may know that the URLs for custom emojis are public and anyone can look at them if they have the URL.
The format of these urls look like this:

`https://emoji.slack-edge.com/T02N6U1DVD4/squirrel/465f40c0e0.png` aka

![A picture of a squirrel wearing a fedora and a suit](https://emoji.slack-edge.com/T02N6U1DVD4/squirrel/465f40c0e0.png)

After some playing around, these urls can be expressed as `${team_id}/${emoji_name}/${cache_buster_hash}.png`

 * base url is always `https://emoji.slack-edge.com`
 * `${team_id}` the identifier for the slack intance
 * `${emoji_name}` is easy and quite guessable cause so many people like to recreate their last work slack in their new place, like `:falcepalm:`
 * `${cache_buster_hash}` is a random 10 character hex string so it can bust some caches

> side note: `:squirrel:`  is a custom emoji on all instances & can be used to confirm an `team_id`

The `${cache_buster_hash}` part seems to be a bit finiky. For `:squirrel:` it can be **any** value.
So `0000000000` is perfectly valid and is great to verify a `${team_id}`

## Ok, but so what? 

Well, it is handy to know and if you ever want to spook your security team
just let them know that employee PII (photos) in the form of emojis are publicly
accessible with three easy steps:

 1. Post the emoji in question
 1. `Right click > Copy Image Link`
 1. Open that link in a private window / incognito window
 1. 🤯🧑‍💻 <!-- I hope that ZWJ works everywhere -->

Though, you do need to have 3 pieces of information to get to it without it getting help from the inside.

I did take this to the Slack enterprise people once in a past job.
The response was: "Working as designed".

## What can I do really do with this?

Well, not that much... you need to know the `${team_id}` of the slack instance you are interested in and sometimes the `${cache_buster_hash}`.

That is hard if you didn't save it somewhere before you got kicked off for spamming `:flag-ua::muscle::flag-ua:`

So far I have not found any unauthenticated way to get that `${team_id}` but the `:squirrel:` trick is great
to confirm a slack instance.

## Conculsion / FAQ

Is Slack emojis good for storing important information?

> No, but this is not an invitation to start using GUID's as emoji names nor to stop making wholesome meme of colleagues **with their consent**.

So why are Slack emojis public?

> lol, no idea. Probably cause of some ~legacy~ money generating systems

Should I save my companies Slack's `${team_id}` before I leave so I can download all my emojis later??

> Nah. Just treat your favourte emojis like NFT's, `right-click > Save Image As ...` them cause who knows when the server is going offline.
