Explainer: Font Features Client-Hints Headers
---


Editors:
---
* Daniel LaLiberte (Google LLC)
* Dominik Röttsches (Google LLC)

(Need to involve the Google Fonts folks Rod Sheeter, Evan Adams, and Yoav Weiss. Also involve other font services.  Also solicit feedback from CSS working group folks as well (fantasai, Chris Lilley, Jonathan Kew, Myles Maxfield, Dominik). )

Abstract
---

HTTP Client Hints defines an Accept-CH response header that servers can use to advertise their use of request headers for proactive content negotiation. This explainer introduces a set of client hints (CH) headers regarding font features. These client hints notify the server of font features supported by the client that will meaningfully alter which resources will be returned by the server. 

If appropriate, these client hints may be used as critical client hints via the Critical-CH header.

Status of this document
----

Initial draft.

Why do we care?
---

The short answer that client hints regarding font features are worth considering is to address these goals:

1. We want to avoid extraneous requests for potentially large font resources that are unlikely to be used.
2. We want to avoid the initial delay in rendering due to waiting for the response to asynchronous requests for font resources.
3. We want to avoid significant layout changes once font resources have been loaded.
4. Other goals?

More details on why these concerns are relevant:

* Web servers need to know how to generate responses to client requests containing uses of font resources that the client either already has available to it or that it can request asynchronously and subsequently use correctly. A typical use case, for example, involves a third-party font service provider such as Google Fonts, Adobe Fonts, H&Co or similar, in which the font service would prefer to deliver different font files depending on client capabilities.  So we want clients to be able to report to both web servers and font services sufficient details about client capabilities so that we can ultimately provide the best user experience.

* To support variable fonts, color vector fonts, and other third-party content which requires client information lost by the user agent reduction implementation, we need a way to extend client hints. 
 
* Example: Variable fonts allow significantly less font information to be required to render equivalent displays of text without loss of functionality, but clients may rely on specific operating systems to provide this functionality.

* Example: A client may support COLRv1 color fonts, while also not supporting OT-SVG font files, so the server should send CSS that references COLRv1 files, and not OT-SVG files.

This explainer introduces a set of [CLIENT-HINTS](https://developer.mozilla.org/en-US/docs/Web/HTTP/Client_hints) headers around client support for font features.

Relationship with CSS Font Queries
---

If possible and appropriate, we should make the font feature client hints consistent with what's already specified in the [CSS Fonts Specs (level 4)](https://drafts.csswg.org/css-fonts-4/#font-face-src-parsing): 

The arguments to the format() and tech() functions described in Parsing the 'src' descriptor include a set of capabilities that are useful for making the appropriate decisions on the server side:

```
<url> [ format(<font-format>)]? [ tech( <font-tech>#)]? | local(<font-face-name>)
<font-format>= [<string> | collection | embedded-opentype | opentype
| svg | truetype | woff | woff2 ]
<font-tech>= [<font-feature-tech> | <color-font-tech>
| variations | palettes | incremental ]
<font-feature-tech>= [feature-opentype | feature-aat | feature-graphite]
<color-font-tech>= [color-COLRv0 | color-COLRv1 | color-SVG | color-sbix | color-CBDT ]
```

Relationship with other Features and UA-CHs
---

* Caching of fonts - Even if a large font file is required, if it is already cached, it won’t need to be requested.
* [MathML](https://chromestatus.com/features/5240822173794304)
Uses math fonts, but with more alignment, scaling, grouping, and layout parameters.
* Icons are at the intersection of fonts, small images, and vector graphics.

What is out of scope?
---

What's out of scope for reporting to the server includes: 

The kinds of client side font styling or other typographic capabilities that the client may have that are independent of the client's ability to understand and display certain font files.  For example, which specific CSS-syntax-sugar properties for OpenType features are supported, because …(?? typically, all such properties will be supported if any are. ??)

Other things we now compute based on UA and major version of OS, not specifically font-related (from Rod Sheeter):
- support for stale while revalidate
- support for unicode-range
- support for https://w3c.github.io/resource-hints/#preconnect

There are also a handful of cases where the OS is most or all of the required signal, and hence, a UA-CH is not currently needed:

* The need for hinted fonts; most of the signal is just the OS so the low entropy hints are largely sufficient. For example, Windows always gets hinted fonts whereas Android never does. If a new version of an OS suddenly changes to need or not need hints, the low entropy signal would no longer suffice.
* Definition of fallback fonts to reduce CLS (Cumulative Layout Shift) impact of fonts.  We need to define how to adjust system fonts to have similar metrics to webfonts. System fonts vary from version to version so knowing what platform it is, but not which version, is a problem.
* The need for removal of overlap in outlines due to bad outline processing.  This is only an issue on macOS <= X(??).

Font Features to Consider
---

There are many font features that we might consider adding UA-CH Headers for, and for each, we need to consider why we might want to add support for a Client Hint regarding the feature.

* How are we addressing the goals outlined under "Why do we care?"?
* Is there any benefit for the user, or the client, or document server or font server?
* What info is (or is not) already available to the document server or font server. e.g. the os type implies such and such…

Here is a list of font-related features that may be relevant when considering whether a client hint will be beneficial or necessary.

* [Feature: OpenType variable font support](https://chromestatus.com/feature/4708676673732608)
* [Feature: CSS font-feature-setings](https://chromestatus.com/feature/5831574356492288) Also: https://caniuse.com/font-feature
* [Feature: CSS font-synthesis property](https://chromestatus.com/feature/5640605355999232)
* [Feature: Local Font Access](https://chromestatus.com/feature/6234451761692672)
* [Feature: font-variant-numeric](https://chromestatus.com/feature/5716551491649536)
* [Feature: font-variant-caps](https://chromestatus.com/feature/5764191470223360)
* [Feature: size-adjust descriptor for @font-face](https://chromestatus.com/feature/5662073285509120)
* [Feature: COLRv1 Color Gradient Vector Fonts](https://chromestatus.com/feature/5638148514119680)
* [Feature: COLR/CPAL font support](https://chromestatus.com/feature/5897235770376192)
* [Feature: @font-face descriptors to override font metrics](https://chromestatus.com/feature/5651198621253632)
* [Feature: font-optical-sizing](https://chromestatus.com/feature/5685958032752640)
* [Feature: Intervention: WebFonts use adaptive timeouts to take fallback fonts](https://chromestatus.com/feature/5636954674692096)
* [Feature: Markup based Client Hints delegation for third-party content](https://chromestatus.com/feature/5684289032159232)
* [Feature: Supports keyword format in @font-face src descriptor](https://chromestatus.com/features/6214741698543616)

From this list, we propose to consider first the variable fonts (specifically OpenType variable fonts) and color fonts (COLORv1).

Client Hint Names
---
COLRv1 - Color Gradient V1 Fonts.
VariableFonts - Variable Fonts.

Usage Example
---

When a document server responds to a request for a resource that may contain uses of color fonts or variable fonts,
since these font features might not be supported by the client, and since font resources for color fonts and variable fonts might need to be asynchronously requested of font services and loaded into the client, there would subsequently be some delay before they have been requested and the font resources returned in responses become available.

Document server returns a response with requested hints for COLORv1 and VariableFonts

```
Accept-CH: COLRv1, VariableFonts
```

The client can respond with headers for either or both of these client hints along with details about which fonts are available to use (supported vs previously downloaded?).

```
COLORv1: details
VariableFonts: details
```

Since different fonts may be associated with use of both color fonts and variable fonts, the `Vary` header should be included with responses to requests for font resources for color and variable fonts.

```
Vary: COLRv1, VariableFonts
```


FAQ
---

Alternatives Considered
---

Acknowledgements
---

References
---
[CLIENT-HINTS]
Ilya Grigorik; Yoav Weiss. HTTP Client Hints. RFC - Experimental (February 2021; No errata). URL: https://datatracker.ietf.org/doc/rfc8942/

CSS Fonts Module Level 4
Editor’s Draft, 10 December 2021
https://drafts.csswg.org/css-fonts/

​​
[Comparison of browser engines (typography support)](https://en.wikipedia.org/wiki/Comparison_of_browser_engines_(typography_support))

[Can I Use: 50 ‘font’ results](https://caniuse.com/?search=font)

From Dominik R.:
---

Additional useful reading might be:

* [Review Request: CSS Fonts src: descriptor syntax for client side font selection](https://github.com/w3ctag/design-reviews/issues/666)

* [css-fonts-4][css-nesting] Nesting of @supports inside @font-face and font technology feature queries  https://github.com/w3c/csswg-drafts/issues/6520 although these are somewhat lengthy standardization discussions, and they discuss what kind of client side feature detection should be exposed to the web. But similar decisions need to be taken server side in response to UA-CH.

