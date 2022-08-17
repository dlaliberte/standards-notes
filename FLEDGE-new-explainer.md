# FLEDGE Explainer

## Authors:

- [Author 1]
- [Author 2]
- [etc.]

## Participate
- [Issue tracker](https://github.com/WICG/turtledove/issues)
- [Discussion forum](??)
- 
We plan to hold regular meetings under the auspices of the WICG to go through the details of this proposal and quickly make any needed changes. Please comment on the timing question in [Issue #88](http://go/gh/WICG/turtledove/issues/88) if you want to attend these meetings to be involved in hammering out details.


## Table of Contents

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
- [FLEDGE Explainer](#fledge-explainer)
  - [Authors:](#authors)
  - [Participate](#participate)
  - [Table of Contents](#table-of-contents)
  - [Introduction](#introduction)
  - [Goals [or Motivating Use Cases, or Scenarios]](#goals-or-motivating-use-cases-or-scenarios)
  - [Non-goals](#non-goals)
  - [Previous Proposals](#previous-proposals)
  - [[API 1]](#api-1)
  - [[API 2]](#api-2)
  - [Key scenarios](#key-scenarios)
    - [Scenario 1](#scenario-1)
    - [Scenario 2](#scenario-2)
  - [Detailed design discussion](#detailed-design-discussion)
    - [[Tricky design choice #1]](#tricky-design-choice-1)
    - [[Tricky design choice 2]](#tricky-design-choice-2)
  - [Considered alternatives](#considered-alternatives)
    - [[Alternative 1]](#alternative-1)
    - [[Alternative 2]](#alternative-2)
  - [Stakeholder Feedback / Opposition](#stakeholder-feedback--opposition)
  - [References & acknowledgements](#references--acknowledgements)
<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Introduction

[The "executive summary" or "abstract".
Explain in a few sentences what the goals of the project are,
and a brief overview of how the solution works.
This should be no more than 1-2 paragraphs.]

FLEDGE is a [Privacy Sandbox](https://developer.chrome.com/docs/privacy-sandbox/overview/) proposal to serve [remarketing](https://developer.chrome.com/docs/privacy-sandbox/fledge/#remarketing) and custom audience use cases designed so that it cannot be used by third parties to track user browsing behavior across sites. The API enables on-device auctions by the browser, to choose relevant ads for websites the user has previously visited. FLEDGE uses [interest groups](http://go/chrome-fledge#interest-group-detail) to enable sites to display ads that are relevant to their users.

## Goals [or Motivating Use Cases, or Scenarios]

The key idea is that advertisers and publishers will be able to target audiences on the basis of interest groups. Another noteworthy point in this proposal is that the auction-related decisions will take place in the browser as opposed to ad servers, publisher servers, or other third parties.

## Non-goals

[If there are "adjacent" goals which may appear to be in scope but aren't,
enumerate them here. This section may be fleshed out as your design progresses and you encounter necessary technical and other trade-offs.]

## Previous Proposals

FLEDGE keeps the major principles of TURTLEDOVE while it builds upon features of the following list of previous proposals:

 * SPARROW, proposed by Criteo
 * Dovekey, proposed by Google Ads
 * PARRROT, proposed by Magnite (previously Rubicon Project)
 * And, TERN, proposed by NextRoll 


SPARROW and Dovekey both proposed that the bidding and auction decisions should be handled by a third party rather than the browser. This was to prevent walled gardens, such as Google, being in control of the bidding process.

Since trusting a third party with the auction didn’t seem to agree with a lot of parties in the ad tech industry, PARRROT suggested that auction-decisions should be made by the publisher server. This proposal, along with TERN, also provided enhanced clarity regarding the roles of DSPs and SSPs.

## [API 1]

[For each related element of the proposed solution - be it an additional JS method, a new object, a new element, a new concept etc., create a section which briefly describes it.]

```js
// Provide example code - not IDL - demonstrating the design of the feature.

// If this API can be used on its own to address a user need,
// link it back to one of the scenarios in the goals section.

// If you need to show how to get the feature set up
// (initialized, or using permissions, etc.), include that too.
```

[Where necessary, provide links to longer explanations of the relevant pre-existing concepts and API.
If there is no suitable external documentation, you might like to provide supplementary information as an appendix in this document, and provide an internal link where appropriate.]

[If this is already specced, link to the relevant section of the spec.]

[If spec work is in progress, link to the PR or draft of the spec.]

## [API 2]

[etc.]

## Key scenarios

[If there are a suite of interacting APIs, show how they work together to solve the key scenarios described.]

### Scenario 1

[Description of the end-user scenario]

```js
// Sample code demonstrating how to use these APIs to address that scenario.
```

### Scenario 2

[etc.]

## Detailed design discussion

### [Tricky design choice #1]

[Talk through the tradeoffs in coming to the specific design point you want to make.]

```js
// Illustrated with example code.
```

[This may be an open question,
in which case you should link to any active discussion threads.]

### [Tricky design choice 2]

[etc.]

## Considered alternatives

[This should include as many alternatives as you can,
from high level architectural decisions down to alternative naming choices.]


### [Alternative 1]

[Describe an alternative which was considered,
and why you decided against it.]

### [Alternative 2]

[etc.]

## Stakeholder Feedback / Opposition

[Implementors and other stakeholders may already have publicly stated positions on this work. If you can, list them here with links to evidence as appropriate.]

- [Implementor A] : Positive
- [Stakeholder B] : No signals
- [Implementor C] : Negative

[If appropriate, explain the reasons given by other implementors for their concerns.]

## References & acknowledgements


FLEDGE is the first experiment to be implemented in Chromium within the [TURTLEDOVE](http://go/gh/WICG/turtledove) family of proposals. The Privacy Sandbox timeline provides implementation timing information for FLEDGE and other Privacy Sandbox proposals.


[Your design will change and be informed by many people; acknowledge them in an ongoing way! It helps build community and, as we only get by through the contributions of many, is only fair.]

[Unless you have a specific reason not to, these should be in alphabetical order.]

Many thanks for valuable feedback and advice from:

- [Person 1]
- [Person 2]
- [etc.]