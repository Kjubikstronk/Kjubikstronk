# Miodrag Obradovic

Informatics at FH Technikum Wien, after HTL Wien West. Vienna, CET.

I mostly fix other people's bugs. The ones I find interesting pass locally and
fail in CI, or worked last week and nobody admits to touching anything. Almost
every fix below is in a codebase I had never opened before, which turns out to
be most of the job.

## Open source

Merged fixes, mostly found by reading the lists of known-broken tests that
projects check into their own repos and working out why each one is broken.

**[home-assistant/core](https://github.com/home-assistant/core)**, the home automation platform

- [#180532](https://github.com/home-assistant/core/pull/180532): a test had been
  reporting as expected-to-fail for months after the bug it guarded was already
  gone. It used the imperative `pytest.xfail()`, which aborts before the
  assertion instead of running it, so unlike the marker it can never report an
  unexpected pass. An async migration had fixed the bug and removed the
  equivalent markers from a neighbouring file; this one survived because nothing
  could see it. Six parameter sets went back to actually asserting.

**[prettier](https://github.com/prettier/prettier)**, the code formatter

- [#19893](https://github.com/prettier/prettier/pull/19893): `assigned = (a = c /* comment */)`
  moved the comment every time you formatted it, so the file never settled. The fix
  needed the ancestor chain that the comment attacher already built and then threw away.
- [#19894](https://github.com/prettier/prettier/pull/19894): the same instability in
  sequence expressions. Babel starts a `SequenceExpression` at the parentheses of its
  first element, so dropping redundant parens moved the node's start past the comment.
- [#19880](https://github.com/prettier/prettier/pull/19880): range formatting reached
  past the node you selected
- [#19849](https://github.com/prettier/prettier/pull/19849): escaped characters in
  markdown links
- [#19878](https://github.com/prettier/prettier/pull/19878): blockquote markers leaking
  into setext headings

**[jsdom](https://github.com/jsdom/jsdom)**, the DOM implementation a lot of JS testing runs on

- [#4245](https://github.com/jsdom/jsdom/pull/4245): attributes kept pointing at their
  old document after being moved. Fixed by implementing the steps the DOM spec actually
  gives for "append an attribute" and "replace an attribute", which jsdom was missing.
- [#4246](https://github.com/jsdom/jsdom/pull/4246): `getElementsByTagName` kept
  lowercasing after its root moved to an XML document, because the memoised collection
  outlived the document type it was built for

**[astro](https://github.com/withastro/astro)**, the web framework

- [#17742](https://github.com/withastro/astro/pull/17742): a trailing slash disappeared
  when an injected `.html` was stripped, breaking routes

Open PRs at [hono](https://github.com/honojs/hono),
[typescript-eslint](https://github.com/typescript-eslint/typescript-eslint),
[marked](https://github.com/markedjs/marked),
[happy-dom](https://github.com/capricorn86/happy-dom),
[vite](https://github.com/vitejs/vite), [axios](https://github.com/axios/axios),
[date-fns](https://github.com/date-fns/date-fns) and
[storybook](https://github.com/storybookjs/storybook).

## What I'm building

**[raspisentry](https://github.com/Kjubikstronk/raspisentry)**, a Raspberry Pi that
turns its camera to follow your face and emails you the frame when it sees one.
OpenCV for detection, a Pan-Tilt HAT for the servos, split across two processes: one
on the Pi owning the camera and the motors, one serving the dashboard from anywhere
on the network.

**[downforce](https://github.com/Kjubikstronk/downforce)**, the F1 championship as a
descent, where scroll distance between teams is the points gap, to scale. A
111-point deficit is a long fall through the dark. A one-point deficit is two pixels.

**[dateideas](https://github.com/Kjubikstronk/dateideas)**, a private date planner for
two. GitHub Pages serves everything publicly, so the site is deliberately an empty
shell; the data lives behind Firestore rules keyed to two UIDs.

**[press-it](https://github.com/Kjubikstronk/press-it)**,
**[swift-archive](https://github.com/Kjubikstronk/swift-archive)**,
**[nct-127](https://github.com/Kjubikstronk/nct-127)**, auto-updating music archives
on a shared static build. Discography, videos and news refresh on a schedule, so the
sites stay current without anyone touching them.

**SALURO**, anonymous payments prototype for school use: Spring Boot and a JS front
end behind Docker Compose. My diploma project, so the repo stays private.

## Working with me

JavaScript, TypeScript, Node, Python, Java and Spring. English or German.

I take contained bug fixes from 60 EUR, agreed before I start and only once I
have seen a reproduction. If I cannot reproduce it, you owe me nothing, because
I will not have done anything worth billing for.

Send me the repository and the reproduction: **mck097@gmail.com**
