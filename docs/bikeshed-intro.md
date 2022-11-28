Intro to Writing Specs with Bikeshed
-----------------

- [Intro to Writing Specs with Bikeshed](#intro-to-writing-specs-with-bikeshed)
- [Initial setup](#initial-setup)
  - [Using GitHub.](#using-github)
- [Spec writing overviews](#spec-writing-overviews)
- [Strategies for incremental development](#strategies-for-incremental-development)
- [Starting Templates](#starting-templates)
- [Sample Full Specifications following Best Practices](#sample-full-specifications-following-best-practices)
- [Other Intros](#other-intros)
  - [Not specific to Bikeshed](#not-specific-to-bikeshed)

## Initial setup

[Domenic's guide to spec excellence - Docs](http://doc/1cRVD1k-hDBGfLVwTG14P_ZqJLM4d5-Z4vpwYFb_4qks#heading=h.qc07m2oa0jm)
(mostly tactical "how to start a new spec")
### Using GitHub.

Initially create an empty or mostly empty spec.bs file (e.g. [Minimal template](http://go/gh/WICG/starter-kit/blob/main/templates/index.bs)_), and associated files to automatically process updates to test the spec file and regenerate html.

See [Spec Prod Documentation](https://w3c.github.io/spec-prod/)

## Spec writing overviews

[How to read, write, and think about specs - Slides](http://go/how-to-specs#slide=id.p)


## Strategies for incremental development

If your API code already exists, and you have WebIDL specifications for that code, a great place to start is to copy in a subset of the WebIDL that corresponds to the public API.

After that, then you can add infra specs that reference the WebIDL specs.

## Starting Templates

Different types of APIs could have specs that follow different patterns.  Here is a list of several:

* [Sections template](http://go/gh/WICG/starter-kit/tree/main/templates)
* [Minimal template](http://go/gh/WICG/starter-kit/blob/main/templates/index.bs)
...

## Sample Full Specifications following Best Practices

* [Navigation API](https://wicg.github.io/navigation-api/)
* [Prioritized Task Scheduling](https://wicg.github.io/scheduling-apis/)
* [Close Watcher API](https://wicg.github.io/close-watcher/)

## Other Intros

A very helpful document, by Gary Kac, similar in purpose to this one:
[Writing Procedural Specs](https://garykac.github.io/procspec/)

### Not specific to Bikeshed

[Writing Promise-Using Specifications](https://www.w3.org/2001/tag/doc/promises-guide)

[Draft Spec - Read Write Web Community Group](https://www.w3.org/community/rww/wiki/Draft_Spec)

[QA Framework: Specification Guidelines](http://go/w3cstd/qaframe-spec/)

[Chromium Specification Mentors](http://go/chromium-spec-mentors)

[Home | Internet-Draft Author Resources](https://authors.ietf.org/)

[Web Platform Design Principles](https://w3ctag.github.io/design-principles/)
