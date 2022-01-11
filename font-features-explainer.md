Explainer: Font Features User-Agent Client-Hints Headers
---


Editors:
---
Daniel LaLiberte (Google LLC)
Dominik Röttsches (Google LLC)

(Need to involve the Google Fonts folks Rod Sheeter and Evan Adams - and solicit feedback from CSS working group folks as well (fantasai, Chris Lilley, Jonathan Kew, Myles Maxfield, Dominik). )

Abstract
---

HTTP Client Hints defines an Accept-CH response header that servers can use to advertise their use of request headers for proactive content negotiation. This explainer introduces a set of user preference font features client hints headers like Sec-CH-Prefers-Font-..., which notify the server of user preferences that will meaningfully alter the requested resource, like, for example, through the currently preferred font thing. These client hints will commonly also be used as critical client hints via the Critical-CH header.

Status of this document
----

Why do we care?
---

The short answer is:

1. We want to avoid extraneous requests for potentially large font resources that are unlikely to be used.
2. We want to avoid delay in rendering due to waiting on asynchronous requests for font resources.
3. We want to avoid significant layout changes once font resources have been loaded.
4. Other reasons?

More details on why these concerns are relevant:

* CSS font queries, and specifically font Queries 5 §11 User Preference font Features like prefers-color-scheme or prefers-reduced-motion, have a potentially significant impact on the amount of CSS that needs to be delivered by a page, and on the experience the user is going to have when the page loads.  The two major kinds of negative impacts involve the initial delay in rendering while waiting for required font files to be downloaded, and the subsequent redraws that might result when font changes require changes to page layout.

* Web servers need to know how to generate a minimal response to client requests with the right fonts so that the client can interpret these font files correctly. The usual use case is, for example, a third-party font service provider such as Google Fonts, Adobe Fonts, H&Co or similar. The service may want to deliver different font files depending on client capabilities. So we want clients to report to the server what font file types the client understands. 

* To support variable fonts, color vector fonts, responsive images, and other third-party content which requires client information lost by the user agent reduction implementation we need a way to extend client hints. For example: variable fonts allow significantly less font information to be transferred without loss of functionality, but only works on specific operating systems.

* Example: A client does support COLRv1 color fonts, but does not support OT-SVG font files, so the server should send CSS that references COLRv1 files, and not OT-SVG files.

* Example: High-traffic sites like search engines or news sites that wish to honor user preference font features like prefers-color-scheme and that inline CSS for performance reasons need to know about the preferred color scheme (or other user preference font features respectively) ideally at request time, so that the initial HTML payload already has the right CSS inlined. Additionally, and specifically for prefers-color-scheme, sites by all means want to avoid a flash of inaccurate color theme. 

This explainer introduces a set of [CLIENT-HINTS] headers around user preference font features as defined by [...].

We should make the font feature client hints consistent with what's already spec'd in the [CSS Fonts Specs (level 4)](https://drafts.csswg.org/css-fonts-4/#font-face-src-parsing): 

The arguments to the format() and tech() functions described in Parsing the 'src' descriptor include a set of capabilities that are useful for making the right decisions on the server side:

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

The kind of client side font styling or other typographic capabilities that the client may have that are independent of the client's ability to understand and display certain font files.  For example, which specific CSS-syntax-sugar properties for OpenType features are supported, because …(?? typically, all such properties will be supported if any are. ??)

Other things we compute based on UA and major version of UA, not specifically font-related (from Rod Sheeter)
- support for stale while revalidate
- support for unicode-range
- support for https://w3c.github.io/resource-hints/#preconnect

There are also a handful of cases where the OS is most or all of the required signal, and hence, a UA-CH is not currently needed:

* The need for hinted fonts; most of the signal is just the OS so the low entropy hints are largely sufficient. For example, Windows always gets hinted fonts whereas Android never does. If a new version of an OS suddenly changed to need or not need hints, the low entropy signal would no longer suffice.
* Definition of fallback fonts to reduce CLS (Cumulative Layout Shift) impact of fonts.  We need to define how to adjust system fonts to have similar metrics to webfonts. System fonts vary version to version so knowing what platform it is, but not what version, is a problem
* The need for overlap removal in outlines due to bad outline processing, only on macOS <= X(??)

Font Features to Consider
---

Answer these questions for each following font features we might consider adding UA-CH Headers for:

* Why include this font feature in a Client Hint?  
  * How are we addressing the goals outlined under "Why do we care?"?
  * Is there any benefit for the user (or the server) to support CHs for this feature?
* What info is not already available to the server. e.g. the os type implies such and such…


[Feature: OpenType variable font support](https://chromestatus.com/feature/4708676673732608)

[Feature: CSS font-feature-settings](https://chromestatus.com/feature/5831574356492288) Also: https://caniuse.com/font-feature

[Feature: CSS font-synthesis property](https://chromestatus.com/feature/5640605355999232)

[Feature: Local Font Access](https://chromestatus.com/feature/6234451761692672)

[Feature: font-variant-numeric](https://chromestatus.com/feature/5716551491649536)

[Feature: font-variant-caps](https://chromestatus.com/feature/5764191470223360)

[Feature: size-adjust descriptor for @font-face](https://chromestatus.com/feature/5662073285509120)

[Feature: COLRv1 Color Gradient Vector Fonts](https://chromestatus.com/feature/5638148514119680)

[Feature: COLR/CPAL font support](https://chromestatus.com/feature/5897235770376192)

[Feature: @font-face descriptors to override font metrics](https://chromestatus.com/feature/5651198621253632)

[Feature: 'font-display: optional' without relayout](https://chromestatus.com/feature/6386634616471552)

[Feature: font-optical-sizing](https://chromestatus.com/feature/5685958032752640)

[Feature: Intervention: WebFonts use adaptive timeouts to take fallback fonts](https://chromestatus.com/feature/5636954674692096)

[Feature: Markup based Client Hints delegation for third-party content](https://chromestatus.com/feature/5684289032159232)

[Feature: Supports keyword format in @font-face src descriptor](https://chromestatus.com/features/6214741698543616)

Usage Example
---

Sec-CH-UA-COLRv1 - indicates whether the client supports Color Gradient Ve ctor Fonts.

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

