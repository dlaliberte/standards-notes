Explainer: Font Features Client-Hints Headers
---


Editors:
---
* Daniel LaLiberte (Google LLC)
* Dominik Röttsches (Google LLC)

Abstract
---

HTTP Client Hints defines an Accept-CH response header that servers can use to advertise their use of request headers for proactive content negotiation. This explainer introduces a set of client hints (CH) headers regarding font features. These client hints notify the server of font features supported by the client that will meaningfully alter which resources will be returned by the server. 

If appropriate, these client hints may be used as critical client hints via the Critical-CH header.

Status of this document
----

Initial draft.

Why do we care?
---

Client hints regarding font features are worth considering to address the following goals:

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

When a document server responds to a request for a resource that could contain uses of color fonts or variable fonts, even if these font features are supported by the client, since font resources for color fonts and variable fonts might need to be asynchronously requested of font services and loaded into the client, there would subsequently be some delay before they have been requested and the font resources returned in responses become available.  So the document server needs to know as soon as possible when responding to a request for a document whether the client supports font features that it might rely on.


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
* The need for removal of overlap in outlines due to bad outline processing. This is only an issue on versions of macOS as follows:
  * 10.11 and below needs overlaps removed
  * 10.12 needs the overlap bit set
  * 10.13 and above supports variations fonts


Use Cases
---
A typical flow between the client and multiple servers might look like this:

1. The client first requests a resource. 
2. The server responds with that resource that also references other resources (e.g. css files), and includes the Accept-CH: VariableFonts header, as a request to the client. 
3. The client then could request css files from the same server, and in this "response" it includes the VariableFonts: true header.  
4. Then the server responds with a css file that depends on using variable fonts.  
5. Then the client requests the variable it doesn't yet have from Google Fonts.
6. Google Fonts returns the requested variable fonts.

Bugs discovered in browsers regarding font features (or any other browser features, for that matter) may need rapid response to identify the bugs in client hints so that services such as Google Fonts can respond accordingly. E.g. the Mac outline overlap bug needs to be identified when it applies.  It ought to be simpler to add a client hint to the next release of a browser that identifies the relevant bugs than to actually fix or workaround the bugs.

Usage Example
---

Document server returns a response to a request for a document with a request of client-hints regarding COLORv1 and VariableFonts:

```Accept-CH: COLRv1, VariableFonts```

The client can respond with headers for either or both of these client hints along with details about which fonts are available to use (supported vs previously
downloaded?).

```
COLORv1: details
VariableFonts: details
```

Since different fonts may be associated with use of both color fonts and variable fonts, the Vary header should be included with responses to requests for font resources
for color and variable fonts.

Client Hint Names
---
* COLRv1: support for rendering colour font glyphs specified in a COLRv1 table. Specification for COLRv1 and COLRv0 is found here: https://docs.microsoft.com/en-us/typography/opentype/spec/colr
* VariableFonts: support for rendering variable fonts. Specification is found here: https://docs.microsoft.com/en-us/typography/opentype/spec/otvaroverview

Future client hints to consider:
* CBDT/CBLC
* SBIX
* COLRv0
* OT-SVG


Usage Example
---


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
CSS Font Queries
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

While the use of src/tech() is ideal for small use-cases (when you just have a couple versions of a font to
potentially use, like "basic" and "good"), in the case of services like Google Fonts, there could be dozens of variants, 
because they can support many features that might all be differently supported by each client based on OS config, so it becomes a
combinatorial explosion.  Let's consider 2-position values for color, variations, incremental transfer, 
the Mac Overlap bug, 4 file formats, and 3 codecs.  That's a total of 192 combinations and we definitely don't want to list 192 src 
entries for each combination.


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

Additional useful reading might be:

* [Review Request: CSS Fonts src: descriptor syntax for client side font selection](https://github.com/w3ctag/design-reviews/issues/666)

* [css-fonts-4][css-nesting] Nesting of @supports inside @font-face and font technology feature queries  https://github.com/w3c/csswg-drafts/issues/6520 although these are somewhat lengthy standardization discussions, and they discuss what kind of client side feature detection should be exposed to the web. But similar decisions need to be taken server side in response to UA-CH.
