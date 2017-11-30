---
layout: post
title: "Inherent problems in the Magento Ecosystem"
date: 2017-11-30 13:15:49
image: '/assets/img/'
description: 'Seems no one follows Magento Standard Practices'
main-class: 'general'
color: '#222'
tags:
- Magento 2
- Community
- Problems
categories:
- Magento 2
twitter_text:
introduction: 'Seems no one follows Magento Standard practices'
---

I have spent some time working on Magento 2, in fact I am taking the Magento 2 certification exam this month. I started pondering a few questions as I study for the exam. What does having the certification actually mean? Why do so many people rely on it to make an informed decision about a developer or agency?

Let me be the first to tell you, the Magento Certification means almost absolutely nothing. I have a unique perspective from a work vantage point where I get to inspect code from all backgrounds, including major Magento development agencies with high profiles.

What I have unfortunately come to terms with is--Almost no one, freelance developers and agencies alike, follows Magento Standard Practices, and certainly no one writes unit tests or even tests their integrations!

What does this mean for you? Well, you get severe performance penalties, unmaintainable code, and developers who have no understanding at all of Magento Architecture--Even if they have the certification.

Magento spent a lot of time and effort writing the developer docs for Magento 2. In fact they are amazing, if you read and follow them. They tell you everything you ever need to know about Magento 2 development.

Parts of the devdocs that every agency and developer I have come into contact with seemingly forgot to read:

1. [Coding Standards](http://devdocs.magento.com/guides/v2.2/coding-standards/bk-coding-standards.html)
That is right, Magento spells out coding standards that must be adhered to to effectively write proper maintainable code for Magento 2. It is NOT followed.
2. [Technical Guidlines](http://devdocs.magento.com/guides/v2.2/coding-standards/technical-guidelines/technical-guidelines.html)
Technical Guidlines that explain everything you need to know about writing Magento 2 code. Again, skipped apparently, because I have yet to come across any agencies or developers that have used the design principles in this document to design there code.
3. [Definition of Done](http://devdocs.magento.com/guides/v2.2/contributor-guide/contributing_dod.html)
Another one of those very important documents that should be adhered to. Docblocks are not optional!
4. [Testing](http://devdocs.magento.com/guides/v2.2/test/testing.html)
Testing should be the most important aspect of any development cycle. I have yet to find any unit tests used by agencies or developers.

## The unfortunate realization to me is, there are too many hacks, posers, and incompetent developers and agencies in the Magento Ecosystem.

Magento is not a framework that can be hacked on and expect performant results. There are methodologies and certain ways things need to be done in order to not hurt code performance or functionality in other areas.

*Please review any developer or agency you use. Magento certification means nothing really. Most of the questions review simple navigation of frontend and admin, nothing so in-depth that it covers what developers should really know. It is also multiple choice, so it can be passed very easily.*


## How is the Magento Community dealing with the onslaught of posers and hacks?

There really isn't an easy answer to this question. There is [commercehero.io](https://commercehero.io) which uses community ratings to attempt to weed out the dysfunctional developers.

### The single most problem I've seen is agencies and developers lack of understand of how to keep Magento and PHP performant while writing code. Magento has very implicit ways of dealing with the database and this is time and again ignored.

