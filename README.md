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
- [#19939](https://github.com/prettier/prettier/pull/19939): an own-line comment before a
  `satisfies` or `as` type got pulled onto the line above, then moved again on the next
  format, so the file never settled. Two rounds of review on this one, including reverting
  a commit I had been too clever with.
- [#19930](https://github.com/prettier/prettier/pull/19930): a comment at the end of
  a parenthesized arrow chain escaped the parentheses on the next format. The chain
  walker only stopped at an expression statement, so nothing inside a `var`
  declaration ever found a statement to attach the comment to.
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
- [#4244](https://github.com/jsdom/jsdom/pull/4244): filling in the text of an
  already-connected `<script>` never ran it, because jsdom was missing the DOM spec's
  children-changed steps for script elements. Several scripts inserted together could
  then run out of order. Removal, and an element still mid-parse, both opt out, matching
  how style elements already work.

**[marked](https://github.com/markedjs/marked)**, the markdown parser

- [#4053](https://github.com/markedjs/marked/pull/4053): autolinks and inline links
  resolve character references differently under CommonMark, but marked escaped both
  destinations the same way, so fixing one broke the other. Autolinks now get their
  own escaping path.
- [#4074](https://github.com/markedjs/marked/pull/4074): a fenced code block indented
  less than its own fence kept all of its indentation instead of losing what CommonMark
  says it should, because the strip only fired for lines indented at least as far as
  the fence.

**[astro](https://github.com/withastro/astro)**, the web framework

- [#17742](https://github.com/withastro/astro/pull/17742): a trailing slash disappeared
  when an injected `.html` was stripped, breaking routes

**[supabase-js](https://github.com/supabase/supabase-js)**, the JS client for Supabase

- [#2641](https://github.com/supabase/supabase-js/pull/2641): a failed passkey
  registration cleans up after itself by unenrolling the same-named factor, but the
  lookup matched an already-verified factor instead of the unverified one the failed
  attempt left behind. A failed re-registration could delete a passkey that already
  worked. One line, inverted condition.

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

Available for bug fixes and debugging work. Send me the repository and a way to
reproduce it: **mck097@gmail.com**
