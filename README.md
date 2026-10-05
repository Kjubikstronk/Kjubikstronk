# Miodrag Obradovic

Informatics at FH Technikum Wien, after HTL Wien West. Vienna, CET.

I mostly fix other people's bugs. The ones I find interesting pass locally and
fail in CI, or worked last week and nobody admits to touching anything. Almost
every fix below is in a codebase I had never opened before, which turns out to
be most of the job.

Some of it has shipped with my name on it: [jsdom 30.1.0](https://github.com/jsdom/jsdom/releases/tag/v30.1.0),
[babel 8.0.5](https://github.com/babel/babel/releases/tag/v8.0.5) and [astro 7.2.4](https://github.com/withastro/astro/releases/tag/astro%407.2.4).

## Open source

Merged fixes, mostly found by reading the lists of known-broken tests that
projects check into their own repos and working out why each one is broken.

**[node](https://github.com/nodejs/node)**, the JavaScript runtime

- [#66421](https://github.com/nodejs/node/pull/66421): inspecting a detached `DataView`
  left the indentation raised for everything printed after it in the same call.
  `formatExtraProperties()` bumped the indent level before reading the property, and a
  detached view's getters throw, so the level never came back down. Reading the value
  first fixed it. Found while porting the same inspect code to Deno.

**[prettier](https://github.com/prettier/prettier)**, the code formatter

- [#19908](https://github.com/prettier/prettier/pull/19908): a code block inside an
  `md` template is fenced with `~`, and the fence has to be longer than any run of `~`
  in its value. The escaped backticks of a block nested one level deeper turn into
  tildes when they are printed, so the run they end up occupying was never counted and
  the outer fence failed to clear the inner one.
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

**[babel](https://github.com/babel/babel)**, the JavaScript compiler

- [#18214](https://github.com/babel/babel/pull/18214): an optimisation that collapses a
  destructuring declaration and its emptiness check into one statement only looked at the
  shape of the two statements, not at which variable the check was about. So
  `const [{ a }, {}] = [x, y]` lost the `a` binding and checked the wrong value, and
  `const [{}] = [null, {}]` stopped throwing where native code does.

**[marked](https://github.com/markedjs/marked)**, the markdown parser

- [#4053](https://github.com/markedjs/marked/pull/4053): autolinks and inline links
  resolve character references differently under CommonMark, but marked escaped both
  destinations the same way, so fixing one broke the other. Autolinks now get their
  own escaping path.
- [#4080](https://github.com/markedjs/marked/pull/4080): a line indented with two spaces
  and a tab kept the tab as code. The indent regex tried the spaces branch first and
  alternation takes the first match, so the tab branch only ever ran on lines with no
  leading space at all. Swapping the two branches fixed it.
- [#4075](https://github.com/markedjs/marked/pull/4075): the indentation after a hard line
  break was rendered instead of dropped. marked's own test suite couldn't see it, because
  its comparison ignores whitespace, which is why the spec examples had been passing all
  along.
- [#4076](https://github.com/markedjs/marked/pull/4076): numeric character references
  like `&#35;` went out exactly as written instead of as the character they name.
- [#4074](https://github.com/markedjs/marked/pull/4074): a fenced code block indented
  less than its own fence kept all of its indentation instead of losing what CommonMark
  says it should, because the strip only fired for lines indented at least as far as
  the fence.
- [#4073](https://github.com/markedjs/marked/pull/4073): `Renderer.code` appended a
  newline unconditionally, so an empty fence rendered as a code element containing a
  blank line instead of nothing at all. The lexer was already right, the token text
  was empty the whole time.

**[fastify](https://github.com/fastify/fastify)**, the Node web framework

- [#7001](https://github.com/fastify/fastify/pull/7001): `reply.compileSerializationSchema(schema)`
  handed a custom serializer compiler `null` for the status code and content type it was
  never given, where the types, the docs and the neighbouring `serializeInput` path all
  said `undefined`. Dropping the two default values was the whole fix.

**[home-assistant/core](https://github.com/home-assistant/core)**, the home automation platform

- [#180532](https://github.com/home-assistant/core/pull/180532): a test had been
  reporting as expected-to-fail for months after the bug it guarded was already
  gone. It used the imperative `pytest.xfail()`, which aborts before the
  assertion instead of running it, so unlike the marker it can never report an
  unexpected pass. An async migration had fixed the bug and removed the
  equivalent markers from a neighbouring file; this one survived because nothing
  could see it. Six parameter sets went back to actually asserting.

**[astro](https://github.com/withastro/astro)**, the web framework

- [#17742](https://github.com/withastro/astro/pull/17742): with `build.format: 'preserve'`
  and `trailingSlash: 'always'`, stripping the `.html` astro injects also dropped the
  trailing slash the route pattern needed, so dynamic routes failed the build with
  `Missing parameter`

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
