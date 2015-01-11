---
title: CommonMark
---

(The official specification for CommonMark is at <http://jgm.github.io/stmd/spec.html> - as it is written in
HTML that cannot be pasted into a Markdown-compatible Jekyll page, the content in this page instead describes
it in terms of the [vfmd](/vfmd/) specification.)

# CommonMark syntax vs. vfmd syntax

This page attempts to compare the syntaxes of [CommonMark] and [vfmd]
from the perspective of a Markdown document writer. The background for
this comparison is [this HackerNews conversation][HN] between John
MacFarlane, the primary author of the [CommonMark] spec, and Roopesh
Chander, the author of the [vfmd] spec.

Here are links to the syntaxes being compared:
[CommonMark][CommonMark syntax] and [vfmd][vfmd syntax].

[CommonMark]: http://commonmark.org
[CommonMark syntax]: http://jgm.github.io/stmd/spec.html
[vfmd]: http://www.vfmd.org/
[vfmd syntax]: http://www.vfmd.org/vfmd-spec/syntax/
[HN]: https://news.ycombinator.com/item?id=8267039

## Philosophy

While both CommonMark and vfmd attempt to provide an unambiguous
specification for Markdown, their goals and priorities appear to
be somewhat different:

 1. **Readability vs. ease-of-parsing**

    One of CommonMark's [stated goals][CommonMark_goal] is to make
    the syntax "easier to parse".

    For vfmd, ease-of-parsing is not a [goal][vfmd_goals] at all and
    that does not come in the way of designing the syntax. Readability,
    and behaving intuitively and consistently are prioritized over
    ease-of-parsing.

 2. **Extras**

    As of writing this, there doesn't seem to be a documented policy on
    what syntax constructs CommonMark wants to eventually cover in their
    spec, what it wants to support as extensions, and how the extensions
    should be integrated.

    CommonMark already includes fenced code blocks, and might possibly
    include [other][extra_talk_1] [constructs][extra_talk_2] in the
    future. There's also some discussion on
    [CommonMark extensions][extra_talk_ext].

    vfmd specifies only the syntax constructs that were defined in John
    Gruber's original Markdown, and includes information on how
    additional syntax constructs can be added, while remaining in control
    of how the additional constructs impact handling of the the core
    constructs.

    vfmd [aims to provide a core-syntax-only-spec][vfmd_goals] that
    enable different Markdown flavours to behave consistently for
    the core syntax constructs, while allowing them to diverge in
    their own specialized syntax constructs.

[extra_talk_1]: http://talk.commonmark.org/t/81/12
[extra_talk_2]: http://talk.commonmark.org/t/115/8
[extra_talk_ext]: http://talk.commonmark.org/category/extensions
[vfmd_goals]: http://www.vfmd.org/introduction/#goals
[CommonMark_goal]: http://commonmark.org/#how

## Similarities

There are some aspects in which both CommonMark and vfmd have picked
similar ways to diverge from the original Markdown syntax:

 1. **Lists:**
     -  Both CommonMark and vfmd use a vertical-alignment-based list
        content indentation instead of the [4-space rule]
     -  In ordered lists, the first number is used as the starting
        number of the list
     -  In unordered lists, changing the list bullet character starts
        a new list
     -  Two blank lines can be used to end a list (and all sublists)
 2. **HTML blocks:**
     -  In both CommonMark and vfmd, a HTML `div` (or similar) block ends at
        the next blank line, rather than at the next closing `div` tag

[4-space rule]: http://www.vfmd.org/differences/#lists-and-4-space-rule

## Major syntax differences

These differences are "major" in the sense that these can potentially
require significant changes in one spec to make it behave like the other
spec.

### Block elements

 1. **"Loose" and "tight" in lists:**

    In CommonMark, "loose" and "tight" [apply to the whole
    list][cm_lists], while in vfmd, they [apply to individual list
    items][vfmd_lists].

    For example, consider:

    ~~~
    * One
    * Two

    * Three

    * Four

    * Five
    * Six
    ~~~

    CommonMark considers the above _list_ to be "loose", and for HTML
    output, wraps all list items in `<p>` tags.

    vfmd considers only the _list items_ "Three" and "Four" to be
    "loose", and wraps only those list items in `<p>` tags.

 2. **Start of HTML blocks:**

    CommonMark requires that HTML-block-starting tags (like `div`
    tags) be placed at the [start of a line][cm_html_block]; vfmd handles HTML block
    tags [anywhere in a line][vfmd_html_block], and includes any previous text as part
    of the block.

    For example, consider:

    ~~~
    Some text and
    then suddenly, a <div> *starts*

    Inside the `div`, but separated by blank lines

    </div>
    ~~~

    CommonMark treats the first chunk as a normal paragraph, and the
    HTML output from CommonMark is not valid HTML:

    ~~~
    <p>Some text and
    then suddenly, a <div> <em>starts</em></p>

    <p>Inside the <code>div</code>, but separated by blank lines</p>

    </div>
    ~~~

    vfmd recognises the `<div>` even if it's not at the start of the line,
    and the HTML output from vfmd looks like:

    ~~~
    Some text and
    then suddenly, a <div> *starts*

    <p>Inside the <code>div</code>, but separated by blank lines</p>

    </div>
    ~~~

 3. **HTML blocks that can have blank lines:**

    vfmd allows `pre` / `script` / `style` elements [to contain blank
    lines][vfmd_html_block], but only if well-formed (i.e. iff matching
    opening-closing tags are found). CommonMark does not treat these
    elements specially, and [recommends][cm_html_blocks] using `&#10;`
    in place of a blank line within `pre` elements.

    For example, for the input:

    ~~~
    <pre>
    'Twas brillig, and the slithy toves
        Did gyre and gimble in the wabe;

    All mimsy were the borogoves,
        And the mome raths outgrabe.
    </pre>
    ~~~

    CommonMark outputs the HTML:

    ~~~
    <pre>
    'Twas brillig, and the slithy toves
        Did gyre and gimble in the wabe;
    <p>All mimsy were the borogoves,
    And the mome raths outgrabe.</p>
    </pre>
    ~~~

    while vfmd outputs:

    ~~~
    <pre>
    'Twas brillig, and the slithy toves
        Did gyre and gimble in the wabe;

    All mimsy were the borogoves,
        And the mome raths outgrabe.
    </pre>
    ~~~

    See also: <http://talk.commonmark.org/t/139/>.

[cm_lists]: http://jgm.github.io/stmd/spec.html#lists
[vfmd_lists]: http://www.vfmd.org/vfmd-spec/syntax/#lists

[cm_html_block]: http://jgm.github.io/stmd/spec.html#html-block
[cm_html_blocks]: http://jgm.github.io/stmd/spec.html#html-blocks
[vfmd_html_block]: http://www.vfmd.org/vfmd-spec/syntax/#verbatim-html

### Span elements

 1. **Emphasis:**

    - **Intra-word emphasis:**

      CommonMark allows using '*' for intra-word emphasis, but disallows
      using '_', while vfmd disallows any intra-word emphasis.

    - **Nested emphasis of the same kind:**

      CommonMark is somewhat inconsistent in the treatment of em-within-em
      and strong-within-strong emphasis:

       - `*foo *bar**` is is em-within-em
       - `**foo* bar*` is normal text without any emphasis

      vfmd treats both the above as em-within-em.

      The same difference can be seen for the strong-within-strong scenario
      since CommonMark treats any sequence of four or more `*`s or `_`s
      as normal text. vfmd treats `*` and `_` sequences as potential emphasis
      indicators irrespective of how long they are.

 2. **Span-level HTML:**

    When handling span-level HTML, CommonMark recognizes HTML tags but
    not HTML elements, resulting in some invalid HTML being output.

    For example:

     - `*foo <u>bar* baz</u>*` outputs `<em>foo <u>bar</em> baz</u>*`
     - `*<p>foo</p>*` outputs `<em><p>foo</p></em>`

    vfmd recognizes span-level HTML elements and is aware of HTML
    nesting rules. In the above cases, vfmd chooses to ignore some
    Markdown styling over outputting invalid HTML.

     - `*foo <u>bar* baz</u>*` outputs `<em>foo <u>bar* baz</u></em>`
     - `*<p>foo</p>*` outputs `*<p>foo</p>*` itself

## Minor syntax differences

These differences are "minor" in the sense that, in my assessment, each of
these by itself doesn't require a significant change in one spec to make it
behave like the other spec.

Please note that this is not an exhaustive list.

### Block elements

  - In CommonMark, ATX headers require a space between the "#" and the text, while
    in vfmd, the space is optional.
  - In CommonMark, one blank line ends a blockquote, two consecutive blank lines
    end a list, but code blocks don't end on any number of blank lines.
    In vfmd, two consecutive blank lines end a list / blockquote / code block.
  - In vfmd list syntax, changing the number of spaces before/after the list
    marker starts a new list; In CommonMark, the same list continues.
  - In CommonMark, there's a special-cased rule for allowing the
    first block of a list item to be a code block; In vfmd, there's no special
    rule - this just can't be written using pure vfmd.
  - CommonMark doesn't require a blank line to seprate a top-level
    blockquote from the previous paragraph; vfmd does.
  - CommonMark doesn't require a blank line to seprate a top-level
    list from the previous paragraph; vfmd does.
  - In CommonMark, there's a new "1)" syntax for an ordered list, which
    is absent in vfmd
  - CommonMark recognizes HTML declarations, processing instructions
    and CDATA, while vfmd doesn't - vfmd treats them as text
  - The list of HTML tags that can start a HTML block are different
    between CommonMark and vfmd
  - In vfmd, HTML comments can contain blank lines, while in CommonMark,
    such a blank line would end the block

### Span elements

  - CommonMark allows a link to contain another link label; vfmd doesn't.
  - CommonMark doesn't allow the link URL to be broken across multiple
    lines at all; vfmd allows it if the URL is enclosed in \<angle
    brackets\>.
  - CommonMark allows both inline and reference link titles to be
    specified within parentheses instead of quotes; In vfmd, only
    reference link titles can be specified within parentheses.
  - CommonMark processes the alt text of images as Markdown (not sure if
    this is a bug in the spec or intentional), so
    `![*foo*](url)` becomes `<img src="url"
    alt="&lt;em&gt;foo&lt;/em&gt;" />`; In vfmd, the alt text is
    preserved as is, so the output is `<img src="url" alt="*foo*" />`.
  - CommonMark collapses interior whitespace in code spans into a single
    space; vfmd doesn't.
  - vfmd detects and auto-links URLs of the form `scheme://path` without
    having to enclose them in \<angle brackets\>; CommonMark doesn't.
  - While looking for URLs to be auto-linked, CommonMark has a whitelist of
    schemes that are detected as automatic links; vfmd just looks for any
    text matching a generic `scheme://path` pattern.
  - While looking for email addresses to be auto-linked, CommonMark
    recognizes only ASCII email addresses; vfmd also recognizes
    international email addresses.
  - In CommonMark, backslashes in URLs are escaping backslashes,
    except autolinked URLs; In vfmd, backslashes in all URLs (whether
    autolinked or otherwise) are literal non-escaping backslashes.
  - CommonMark treats a backslash-newline as a substitute for
    space-space-newline (i.e. as a hard line break); vfmd doesn't.

### Encoding

vfmd enforces UTF-8 encoding while CommonMark leaves it unspecified.

However, certain aspects of the CommonMark spec (like link reference lookups)
seem to require a Unicode-based encoding and knowing what the encoding is.
So in practice, CommonMark implementations might have to enforce a
Unicode-based encoding, or auto-detect the encoding, or obtain the
encoding as an input.
