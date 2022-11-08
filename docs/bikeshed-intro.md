Intro to Bikeshed
-----------------

- [Intro to Bikeshed](#intro-to-bikeshed)
- [Initial setup](#initial-setup)
- [Spec writing overview](#spec-writing-overview)
- [Strategy for incremental development](#strategy-for-incremental-development)

## Initial setup

See [Spec Prod Documentation](https://w3c.github.io/spec-prod/)

Create an empty spec.bs file.

## Spec writing overview

[How to read, write, and think about specs - Slides](http://go/how-to-specs#slide=id.p)


## Strategy for incremental development

If your API code already exists, and you have webidl specifications for that code, a great place to start is to copy in the
subset that corresponds to the public API.

Then you can add infra specs that reference the webidl specs.
