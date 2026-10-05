# Miodrag Obradovic

Informatics at FH Technikum Wien, after HTL Wien West. Vienna.

I mostly fix bugs in other people's code, usually in projects I've never opened
before. Some of it has shipped with my name on it: [jsdom 30.1.0](https://github.com/jsdom/jsdom/releases/tag/v30.1.0),
[babel 8.0.5](https://github.com/babel/babel/releases/tag/v8.0.5) and [astro 7.2.4](https://github.com/withastro/astro/releases/tag/astro%407.2.4).

I do a lot of this with an AI coding agent, and I wrote down every way that went
wrong. That became **[slop-free](https://github.com/Kjubikstronk/slop-free)**, a Claude
Code skill for AI-assisted pull requests that maintainers actually want to merge.

## Open source

Merged fixes. A lot of them started from the lists of known-broken tests that
projects keep in their own repos.

**[node](https://github.com/nodejs/node)**

- [#66421](https://github.com/nodejs/node/pull/66421): `util.inspect` raised the indent
  before reading a detached `DataView`'s properties, the getter threw, and everything
  printed after it in the same call came out too far right. I found it while porting
  the same code to Deno.

**[prettier](https://github.com/prettier/prettier)**

- [#19893](https://github.com/prettier/prettier/pull/19893), [#19894](https://github.com/prettier/prettier/pull/19894),
  [#19930](https://github.com/prettier/prettier/pull/19930), [#19939](https://github.com/prettier/prettier/pull/19939):
  comments that moved every time you formatted, so the file never settled. In
  assignments, sequence expressions, parenthesized arrow chains and before
  `satisfies`/`as`. The last one took two rounds of review.
- [#19908](https://github.com/prettier/prettier/pull/19908): code blocks inside `md`
  templates got a fence too short for their content.
- [#19880](https://github.com/prettier/prettier/pull/19880): range formatting reached past the selection.
- [#19849](https://github.com/prettier/prettier/pull/19849): escaped characters in markdown links.
- [#19878](https://github.com/prettier/prettier/pull/19878): blockquote markers leaking into setext headings.

**[jsdom](https://github.com/jsdom/jsdom)**

- [#4245](https://github.com/jsdom/jsdom/pull/4245): attributes kept pointing at their
  old document after a move. jsdom was missing the spec's steps for appending and
  replacing an attribute.
- [#4246](https://github.com/jsdom/jsdom/pull/4246): `getElementsByTagName` kept
  lowercasing after its root moved into an XML document.
- [#4244](https://github.com/jsdom/jsdom/pull/4244): setting the text of a `<script>`
  that was already in the document never ran it.

**[babel](https://github.com/babel/babel)**

- [#18214](https://github.com/babel/babel/pull/18214): an optimisation merged a
  destructuring declaration with its emptiness check without looking at which
  variable the check was for, so `const [{ a }, {}] = [x, y]` lost `a`.

**[marked](https://github.com/markedjs/marked)**

- [#4075](https://github.com/markedjs/marked/pull/4075): indentation after a hard line
  break got rendered. marked's own tests ignore whitespace, which is why nobody saw it.
- [#4053](https://github.com/markedjs/marked/pull/4053): autolinks and inline links need
  different escaping under CommonMark.
- [#4080](https://github.com/markedjs/marked/pull/4080): two spaces and a tab got treated as code.
- [#4076](https://github.com/markedjs/marked/pull/4076): numeric character references like `&#35;` weren't decoded.
- [#4074](https://github.com/markedjs/marked/pull/4074): fenced code indented less than its fence kept too much indentation.
- [#4073](https://github.com/markedjs/marked/pull/4073): an empty fence rendered a blank line.

**[fastify](https://github.com/fastify/fastify)**

- [#7001](https://github.com/fastify/fastify/pull/7001): `compileSerializationSchema`
  passed `null` where the types and docs say `undefined`.

**[home-assistant/core](https://github.com/home-assistant/core)**

- [#180532](https://github.com/home-assistant/core/pull/180532): a test stayed marked as
  expected-to-fail for months after its bug was fixed. `pytest.xfail()` stops before
  the assertion runs, so the test couldn't notice.

**[astro](https://github.com/withastro/astro)**

- [#17742](https://github.com/withastro/astro/pull/17742): `build.format: 'preserve'`
  with `trailingSlash: 'always'` failed dynamic routes with `Missing parameter`.

**[supabase-js](https://github.com/supabase/supabase-js)**

- [#2641](https://github.com/supabase/supabase-js/pull/2641): a failed passkey
  re-registration could delete a passkey that already worked.

## What I'm building

- [raspisentry](https://github.com/Kjubikstronk/raspisentry): a Pi camera on a pan-tilt
  mount that follows your face and emails you a frame. OpenCV, with the camera and
  the dashboard in separate processes.
- [downforce](https://github.com/Kjubikstronk/downforce): the F1 standings as one long
  scroll, where the distance between teams is the points gap.
- [dateideas](https://github.com/Kjubikstronk/dateideas): a date planner for two. The
  site is public, the data sits behind Firestore rules.
- [press-it](https://github.com/Kjubikstronk/press-it), [swift-archive](https://github.com/Kjubikstronk/swift-archive),
  [nct-127](https://github.com/Kjubikstronk/nct-127): music archives that update themselves.
- SALURO: an anonymous payments prototype for school, Spring Boot and Docker. It's my
  diploma project, so the repo is private.

## Work

JavaScript, TypeScript, Node, Python, Java and Spring. English or German.

I take bug fix and debugging work. Send me the repo and a way to reproduce it:
**mck097@gmail.com**
