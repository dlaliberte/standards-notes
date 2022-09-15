Intro to Bikeshed
-----------------

- [Intro to Bikeshed](#intro-to-bikeshed)
- [Initial setup](#initial-setup)
  - [`spec.bs`](#specbs)
  - [`Makefile`](#makefile)
  - [`.gitignore`](#gitignore)
  - [`.pr-preview.json`](#pr-previewjson)
  - [`.github/workflows/build.yml`](#githubworkflowsbuildyml)
- [Development cycle](#development-cycle)

## Initial setup

*Initial “empty” spec.bs with auto-build of spec.html on GitHub.*

There are probably several variations for this could be done.
Example files shown here are for the FLEDGE spec at https://github.com/dlaliberte/turtledove/tree/main

* An admin will need to add a GitHub page for where the spec.html will be written. (need details)

After adding the following files, if there are no errors, the next time spec.bs is updated (or perhaps the first time?), the spec.html file should be auto-generated and will be available via the GitHub page.  E.g. for FLEDGE, the spec.html file will available at
https://wicg.github.io/turtledove/

### `spec.bs`

```html
<pre class="metadata">
Title: FLEDGE
Shortname: fledge
Repository: WICG/turtledove
Inline Github Issues: true
Group: WICG
Status: CG-DRAFT
Level: 1
URL: https://wicg.github.io/fledge/
Boilerplate: omit conformance, omit feedback-header
Editor: Paul Jensen, Google https://www.google.com/, pauljensen@google.com
Abstract: Provides a privacy advancing API to facilitate interest group based advertising.
!Participate: <a href="https://github.com/WICG/turtledove">GitHub WICG/turtledove</a> (<a href="https://github.com/WICG/turtledove/issues/new">new issue</a>, <a href="https://github.com/WICG/turtledove/issues?state=open">open issues</a>)
!Commits: <a href="https://github.com/WICG/turtledove/commits/main/spec.bs">GitHub spec.bs commits</a>
Complain About: accidental-2119 yes, missing-example-ids yes
Indent: 2
Default Biblio Status: current
Markup Shorthands: markdown yes
Assume Explicit For: yes
</pre>
```

### `Makefile`

```Makefile
SHELL=/bin/bash

local: spec.bs
	bikeshed --die-on=warning spec spec.bs spec.html

watch: spec.bs
	bikeshed watch --die-on=warning spec.bs spec.html

spec.html: spec.bs
	@ (HTTP_STATUS=$$(curl https://api.csswg.org/bikeshed/ \
	                       --output spec.html \
	                       --write-out "%{http_code}" \
	                       --header "Accept: text/plain, text/html" \
	                       -F die-on=warning \
	                       -F file=@spec.bs) && \
	[[ "$$HTTP_STATUS" -eq "200" ]]) || ( \
		echo ""; cat spec.html; echo ""; \
		rm -f spec.html; \
		exit 22 \
	);

remote: spec.html

ci: spec.bs
	mkdir -p out
	make remote
	mv spec.html out/index.html

clean:
	rm spec.html
```

### `.gitignore`

```
spec.html
out
```

### `.pr-preview.json`
```json
{
 "src_file": "spec.bs",
 "type": "bikeshed"
}
```

### `.github/workflows/build.yml`

```yml
name: Build
on:
 pull_request:
   branches:
   - main
 push:
   branches:
   - main
jobs:
 build:
   name: Build
   runs-on: ubuntu-latest
   steps:
   - uses: actions/checkout@v2
   - name: Build
     run: make ci
   - name: Deploy
     if: ${{ github.event_name == 'push' && github.ref == 'refs/heads/main' }}
     uses: peaceiris/actions-gh-pages@v3
     with:
       github_token: ${{ secrets.GITHUB_TOKEN }}
       publish_dir: ./out
```

## Development cycle

* Change the spec.bs file.
* Run in local terminal: 
```
curl https://api.csswg.org/bikeshed/ \
	                       --output spec.html \
	                       --write-out "%{http_code}" \
	                       --header "Accept: text/plain, text/html" \
	                       -F die-on=warning \
	                       -F file=@spec.bs`
```
* Preview the spec.html file.
