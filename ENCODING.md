# Encoding & Architecture

Full method-and-design documentation for the *Fides quaerens
intellectum* digital edition prototype. The repository's top-level
[`README.md`](../README.md) is a short orientation; this file is the
canonical record of every encoding decision and architectural
commitment.

The project is a TEI P5 prototype edition of § 1 ("Die Notwendigkeit
der Theologie") of Karl Barth, *Fides quaerens intellectum* (FQI),
as published in volume II/13 of the *Karl Barth Gesamtausgabe* (3rd
ed., Zurich: TVZ, 2002). Model and infrastructure orient themselves
on **heiEDITIONS** (Heidelberger Infrastruktur für digitale Editionen);
see Jakub Šimek, *heiEDITIONS — eine Heidelberger Infrastruktur für
Editionen (nicht nur) mittelalterlicher Texte*, BmE Themenheft 12
(2022), pp. 27–46, and
<https://heieditions.github.io/concepts/concepts.html>.

This document is organized in four parts:

1. **Project context** — why this text, repository layout, file
   naming.
2. **Architectural decisions** — multi-file design, the four-status
   system, the title-page rule, the two discipline rules.
3. **Methodological framework** — the five Šimek textual dimensions
   as actually implemented, plus extensibility hooks.
4. **Technical conventions and operations** — viewer architecture,
   validation, roadmap, license.

---

## Why this text, why this scope

Barth's 1931 Anselm monograph is the methodological hinge between the
dialectical and the dogmatic phases of his theology. § 1 sets the
program of the entire book: theology is *fides quaerens intellectum*,
neither apologetic instrument nor existential safeguard, but the
self-understanding of faith.

The text is, philologically, exactly the kind of source on which a
digital edition can show what print cannot:

- **Three-language interleaving** (German prose, Latin scholastic
  terminology, Latin block-quotations from Anselm in the Schmitt
  edition) that print reproduces typographically (Antiqua / italic /
  letter-spacing) but cannot make queryable.
- **A dense citation network** to Anselm's *Opera Omnia*, with
  citation conventions (`II 47,9` = Schmitt vol. II, p. 47, l. 9) that
  print can only annotate, not link.
- **A four-witness recension** documented by the editors Jüngel and
  Dalferth: the first edition (München 1931), a pre-print of § 1.1 in
  the Kattenbusch festschrift (ZThK 1931), the second author-revised
  edition (Zollikon 1958), and the *Gesamtausgabe* text itself.
- **A theological vocabulary** in which the *same German word* is
  sometimes a technical term (when set in letter-spacing) and sometimes
  not. Apparatus entries documenting the presence or absence of
  letter-spacing therefore document a *semantic* difference, not merely
  a typographic one.

The current encoded scope is **§ 1 *Die Notwendigkeit der Theologie***
(GA II.13, pp. 13–19). The full table of contents of the monograph is
encoded as a *scaffolded* skeleton (see § *Multi-file architecture*
and § *The four-status system* below), so that the demonstrator
displays the encoded section in its actual structural place within
the work, and so that future encoding work can drop into well-named
slots without architectural change.

---

## Repository structure

The repository is organized into four functional layers — the **TOC
layer**, the **content layer**, the **data layer**, and the **viewer
layer**:

### TOC layer (one file)

| File | Purpose |
| --- | --- |
| `fqi-toc.xml` | Single source of truth for the table of contents and for all project-wide metadata: `<listWit>` (the four witnesses A / B / A2 / GA), `<editorialDecl>` (the five-Šimek-dimension declaration), `<projectDesc>`, `<classDecl><taxonomy xml:id="citation-units">`. The viewer fetches this file on start-up. |

### Content layer (part files)

The content of the monograph is partitioned into **part files**, each
of which contains a top-level chapter (or front- / back-matter block)
with all of its `§`-sections as nested `<div>` elements. The
demonstrator currently contains only *one* fully encoded part file:

| File | Status | Contains |
| --- | --- | --- |
| `fqi-part-I.xml` | `kodiert` for § 1; `stub` for §§ 2–5 | Chapter I title page, blank verso, and §§ 1–5 of *Das theologische Programm* (GA pp. 11–72). Only § 1 is fully transcribed; §§ 2–5 are skeletal `<head>` / `<p>` placeholders that exist as anchor targets and as scaffolding for future encoding. |
| `_template_stub.xml` | template | Reference template for further part files (`fqi-frontmatter.xml`, `fqi-part-II-A.xml`, `fqi-part-II-B.xml`, `fqi-anhang.xml`, `fqi-register.xml`). One file rather than 29 individual stub files: a single archetype is enough to demonstrate the encoding pattern, and reviewers do not need 29 near-identical examples. |

The remaining six part files are documented in the TOC with
`<encoding-status>nicht-kodiert</encoding-status>` and `todo:`-prefixed
target URIs. They will be created from `_template_stub.xml` when their
content is encoded. See § *The four-status system* below.

### Data layer (three files)

| File | Purpose |
| --- | --- |
| `terms-heiEDITIONS.xml` | Theological-conceptual register: Latin scholastic terms, Anselmian *mottos*, Barthian German terms, shared dogmatic vocabulary. |
| `persons-heiEDITIONS.xml` | Authority file for persons and corporate bodies (author, subject figures, editors, mentioned scholars, publishers), with GND / VIAF / Wikidata identifiers. |
| `works-heiEDITIONS.xml` | Bibliography of Anselm's works (cited via short titles in the text), reference editions (Schmitt, Migne), and methodological literature. Contains the **editions-layer hook** described in § *Extensibility — Editions Layer*. |

The `-heiEDITIONS` filename suffix on the data files marks them as the
*current* working files of the heiEDITIONS-conformant version; the
older `terms.xml` / `persons.xml` / `works.xml` files preserved in
the repository are the earlier flat-file prototype and are kept only
for historical comparison. New encoding work targets the `-heiEDITIONS`
files exclusively.

### Viewer layer (one file)

| File | Purpose |
| --- | --- |
| `viewer_whole.html` | Single-page web viewer of the edition. Fetches `fqi-toc.xml` on start-up, renders a collapsible left-hand TOC column, and lets the reader navigate between the six display modes (GA Druckansicht, GA Lesefluss, A · 1931, A2 · 1958, B · Vorabdruck, Apparat). On-demand fetches of part files; in-memory cache; URL-hash routing. See § *Viewer architecture*. |

### Documentation

| File | Purpose |
| --- | --- |
| `README.md` | This file. |
| `PROJECT-STATE.md` | Working-session handover protocol (German). Contains the consolidated architecture decisions; consulted at the start of every new working session. |
| `LICENSE` (to come) | CC BY-NC-SA 4.0 for encoding decisions and apparatus; the edited text itself remains under TVZ copyright. |
| `schema/` (to come) | ODD customization and generated RELAX NG / Schematron schemas. |

---

## Multi-file architecture

The edition is split across multiple files for three reasons grounded
in heiEDITIONS practice and Šimek 2022 §§ 3–4:

1. **Selective rendering.** A reader interested in § 1 should not have
   to load the entire monograph. The viewer fetches `fqi-toc.xml`
   on start-up (small, ~60 kB) and then *on demand* fetches whichever
   part file the user navigates to. The in-memory file cache means
   the second visit to a section is instantaneous.
2. **Translation extensibility.** Sibling translation files
   (`fqi-part-I.zh-CN.xml`, `fqi-toc.zh-CN.xml`, ...) can be added
   without touching the original-language files. See § *BCP-47
   translation extensibility*.
3. **Independent versioning of metadata vs. content.** Witness
   declarations, the editorial declaration, and the citation-unit
   taxonomy live in `fqi-toc.xml` and are *not* duplicated across
   part files. A change to the witness apparatus is therefore exactly
   one diff in exactly one file. Part files carry only a thin
   `<teiHeader>` with title-stmt three-tier hierarchy and a
   `<sourceDesc>` whose `<bibl @corresp="fqi-toc.xml#fqi-witnesses">`
   points back to the canonical declaration.

### Single source of truth for part-level metadata

A consequence worth making explicit: **part files do *not* carry
`<title type="part">`-style metadata that the TOC already encodes.**
When the viewer renders a citation suggestion for a § (Chicago 17,
APA 7), the part- and section-title strings are derived from the
active TOC item, not from the part file's `<teiHeader>`. The TOC is
the single authoritative source for navigational and citational labels.

A previous draft of `fqi-part-I.xml` carried a `<title type="part">
Kapitel I. Das theologische Programm</title>` in its `<titleStmt>`,
which had two problems: (i) it duplicated the TOC and would diverge
under future edits; (ii) it introduced a "Kapitel" prefix that the
print source itself does not use in its table of contents or chapter
title page. Both were removed; the TOC's `<ref>` text — `I. Das
theologische Programm` — is the canonical form.

### Hermeneutic discipline

A working rule that follows from the above and that has earned its
own section here because it has been violated in practice:

> **When transcribing TEI from a printed source, do not introduce
> framing labels ("Kapitel", "Abschnitt", structural Latin tags,
> running headers reconstructed from inference) that the source
> itself does not print. If such a label appears in a TEI file
> from a previous edit, it is a bug to be reverted, not evidence
> of the source's wording.**

The single source of truth for what the source says is the source.
TEI files that we ourselves have written are not independent
evidence of the source's wording; they may have accreted infrastructural
labels along the way. When in doubt, the corrective is to revert,
not to defend.

---

## File-naming convention

| Pattern | Meaning |
| --- | --- |
| `fqi-` (prefix) | All project files carry this prefix. `fqi` = *Fides quaerens intellectum*. The prefix is short enough to be readable, long enough to disambiguate against future projects in the same repository. |
| `-heiEDITIONS` (suffix on data files) | Marks the *current* heiEDITIONS-conformant version of a shared data file. Files without this suffix (`terms.xml`, `persons.xml`, `works.xml`) are the earlier flat-file prototype, retained for historical comparison only. |
| `.xml` (extension) | TEI P5 XML. |
| `.zh-CN.xml`, `.en.xml`, ... | Translation siblings. BCP-47 language tag inserted before `.xml`. See § *BCP-47 translation extensibility*. |
| `fqi-toc.xml` | The table-of-contents file. Unique; no sibling part-/section files for the TOC. |
| `fqi-part-I.xml`, `fqi-part-II-A.xml`, `fqi-part-II-B.xml` | Content of the main chapters. `I` / `II-A` / `II-B` follow the monograph's own structural numbering. |
| `fqi-frontmatter.xml`, `fqi-anhang.xml`, `fqi-register.xml` | Content of the structural matter outside the main chapters. |
| `_template_stub.xml` | Underscore-prefixed reference template. Not loaded by the viewer; consulted when creating new part files. |

`xml:id` values inside the files (e.g. `sec-I-1`, `part-II-A`,
`anhang-I`) are **stable across all sibling translation files**: the
German `fqi-part-I.xml#sec-I-1` and a future Chinese
`fqi-part-I.zh-CN.xml#sec-I-1` resolve to *the same conceptual
section*. This is what makes URL-hash routing language-agnostic.

---

## The four-status system

Every TOC entry in `fqi-toc.xml` carries a
`<note type="encoding-status">…</note>` whose value is one of four
strings. The status describes the relation between the TOC entry and
its underlying part-file content:

| Value | Meaning | TOC `<ref>` target |
| --- | --- | --- |
| `kodiert` | The referenced section exists in a part file with a fully transcribed `<body>`. | `fqi-part-I.xml#sec-I-1` |
| `stub` | The referenced section exists in a part file but only as a skeletal `<head>` placeholder. | `fqi-part-I.xml#sec-I-2` |
| `nicht-kodiert` | The referenced part file does not yet exist. The target carries a `todo:` pseudo-URI. | `todo:fqi-part-II-A.xml#sec-II-A-1` |
| `aggregate` | A node that has no body of its own; its content is the concatenation of its sub-nodes. | (no fetchable target) |

A fifth value `titlepage` is reserved for **title-page sub-entries
under an `aggregate` node**: see § *Title-page rule* below.

### Scaffold-not-skip

The architectural principle behind the four-status system is
*scaffold, don't skip*. The TOC encodes the *entire* monograph table
of contents in its real structural shape; sections we have not yet
transcribed are still represented, just with a different status. This
matters because:

- It makes the encoded scope visible *in context*. § 1 of the
  current demonstrator is not a free-floating fragment; it is the
  first section of Chapter I, which is the first of two main chapters,
  which sits inside a structure that includes a frontmatter block, an
  appendix, and a five-register apparatus. The reader sees this
  immediately on opening the viewer.
- It commits the project to a fixed file-naming and `xml:id` regime
  *before* any of the remaining sections are encoded. Late-binding
  decisions about "what should the file containing § II.B.2 be called"
  are avoided because the answer is already in the TOC:
  `fqi-part-II-B.xml#sec-II-B-2`.
- It models heiEDITIONS practice for incomplete corpora. A real
  heiEDITIONS project rarely starts with all sections encoded; it
  starts with a scaffolded TOC and fills it in over time.

### What the viewer does with each status

- `kodiert` and `stub` are **fetchable**: the viewer follows the
  `<ref target>`, fetches the file, scrolls to the anchor.
- `nicht-kodiert` is **clickable but renders a placeholder** with a
  bilingual standby message ("Volltext nicht angelegt" / "Section
  not yet encoded"). The placeholder occupies the same visual slot a
  rendered section would, so the navigation feel does not change.
- `aggregate` follows a **fetchable-fallback** rule: if it has any
  fetchable children (`kodiert` or `stub`), the viewer fetches all of
  them in parallel and renders them concatenated in page order. If
  it has no fetchable children at all, it degrades to a placeholder
  identical in UX to `nicht-kodiert`.

The status is **not shown as an icon next to TOC items** in the
running viewer; it is metadata for the routing logic, not a feature
of the user interface. (An earlier draft of the viewer painted the
four states with coloured glyphs; that version has been retracted as
visually noisy. The TOC-landing-page rendered when the user clicks
the work root *does* still display the symbols as a one-shot status
overview, but the side-column TOC does not.)

---

## Title-page rule

Chapters in the print source typically open on an **odd-numbered
title page** (a right-hand page showing only `I.` and the chapter
title) followed by an **even-numbered blank verso** (`Vakatseite`),
with the actual § 1 starting on the next odd-numbered page. The
title page and its verso belong to the chapter as a unit, not to its
first §.

The convention adopted here is:

- **Title page and Vakat are encoded inside the part file**, as the
  first two `<pb>` elements of `<text><body>` *before* the chapter's
  first `<div type="section">`:
  ```xml
  <body>
    <pb n="11" ed="#GA" type="titlepage"/>
    <head>…chapter title…</head>
    <pb n="12" ed="#GA" type="blank"/>
    <div xml:id="sec-I-1" type="section" n="I.1">…</div>
    …
  </body>
  ```
- `type="titlepage"` and `type="blank"` are *non-standard* `<pb>` types,
  declared in `fqi-toc.xml#encoding-pb-types` (`<editorialDecl>`).
- The viewer renders `type="blank"` as an empty `.ga-page` frame
  (page-number suppressed) in *Druckansicht*, and *suppresses it
  entirely* in *Lesefluss*: a blank page is a phenomenon of the
  printed manifestation, not of the text proper.

This is preferable to the alternative of giving title pages their
own files (`fqi-titlepage-I.xml`, etc.), which we initially considered:
the per-file overhead would not be justified by the few hundred
bytes of content each title page carries, and the chapter-unit
intuition of "title page + verso + first §" would be obscured.

---

## Methodological framework: five textual dimensions

The encoding follows the five textual dimensions proposed by Jakub
Šimek in *heiEDITIONS — eine Heidelberger Infrastruktur für Editionen
(nicht nur) mittelalterlicher Texte* (BmE Themenheft 12, 2022,
pp. 27–46). Šimek's framework is well suited to a portfolio project
because each dimension corresponds to a clearly delimited set of TEI
elements and to a clearly delimited set of user questions a future
edition needs to be able to answer.

### 1. Semantic–logical dimension

The dimension of *Sinneinheiten*: chapter, section, paragraph,
sentence, the hierarchy of meaning that the printed page encodes but
does not name.

- `<div type="chapter" n="I">` for the main chapters. The
  monograph's `Kapitel` corresponds to a TEI `chapter`; the print
  source itself uses Roman numerals (`I.`, `II.`) without the word
  "Kapitel". The TOC follows the source: `I. Das theologische
  Programm` (not `Kapitel I. …`).
- `<div type="section" n="I.1">` for the §-level subdivisions
  (semantic correction over an earlier draft, which used `chapter`
  for §§ themselves).
- `<head><num/><hi rend="#letter-spaced">…</hi></head>` for the
  section title.
- `<p xml:id="p-13-1">` for individual paragraphs, with stable IDs
  derived from the page on which the paragraph begins. This means
  that a paragraph that runs from page 13 to page 14 is still `p-13-1`;
  this is more durable than running paragraph numbers across the
  whole book.

### 2. Visual–material dimension

The dimension of the *Textträger*: pages, layout regions, lines,
typographic distinctions.

- `<pb n="13" ed="#GA" xml:id="ga-13"/>` for page breaks. Two page
  numbering systems run in parallel: `ed="#GA"` marks the
  *Gesamtausgabe* page, `ed="#A"` marks the first-edition page (which
  the GA reproduces in the margin). The viewer's *Druckansicht* uses
  GA-`<pb>` as the boundary of `.ga-page` frames; the `<pb ed="#A">`
  markers remain as inline pale-grey markers (`|15|`) inside the
  running text, signalling first-edition page transitions.
- `<pb type="titlepage">` and `<pb type="blank">` (non-standard
  values, declared in `<editorialDecl>`): see § *Title-page rule*.
- `<hi rend="#letter-spaced">` for *Sperrsatz* (letter-spacing), the
  printer's device by which Barth marks emphasis and by which the
  apparatus tracks variants such as `app-1-d` (where the Sperrsatz
  on *Theologie* is present in GA / A2 but absent in A / B).
- `<hi rend="#italic">` for italic (used by Barth for Latin
  block-quotation and for scholastic terms in running text).
- `<foreign xml:lang="la">` wraps Latin material visually and
  linguistically; see dimension 3.

The `<tagsDecl>` in the `teiHeader` declares CSS-targetable rendition
classes (`#letter-spaced`, `#italic`).

### 3. Linguistic dimension

The dimension of *Sprachen* and (eventually) lemmatized annotation.

- `<language ident="de">` and `<language ident="la">` are declared in
  `langUsage`.
- Every Latin word, phrase, or block-quote carries an explicit
  `@xml:lang="la"` attribute, either on a `<foreign>` element (for
  Latin material inside German running text) or on a `<quote>`,
  `<title>`, or `<term>` element (where the wrapping element is itself
  appropriate).
- A future ODD customization will optionally allow `@lemma` and
  `@pos` on `<w>` for finer-grained linguistic analysis; the
  demonstrator does not require it.

### 4. Extensional dimension — extra-referentiality

The dimension of references to entities outside the text: persons,
works, biblical passages, theological concepts. **This is the
dimension in which the prototype goes farthest beyond a print
edition.**

A methodological refinement of Šimek's account is worth marking
explicitly here, because it underwrites the design of `works.xml`:

> Šimek's fourth dimension speaks of "Extrareferenzialität" —
> reference to entities *outside* the text. For a literary work,
> the relevant external entities are *not* particular editions of
> that work, but the *work* itself in the FRBR sense
> (Functional Requirements for Bibliographic Records, *work / expression
> / manifestation / item*). A reference from Barth to Anselm's
> *Proslogion* is a reference to the work *Proslogion*, not to any
> particular print edition of it. This prototype therefore models the
> *work* directly in `<bibl xml:id="proslogion">`, and treats
> particular editions (the Schmitt critical edition, Migne PL) as a
> *separable manifestation layer* nested inside the work. See
> § *Extensibility — Editions Layer* below.

#### 4a. Persons and corporate bodies — `persons-heiEDITIONS.xml`

Every `<persName>` and `<orgName>` in the part files carries a `@ref`
pointing to an `xml:id` in `persons-heiEDITIONS.xml`. Each entry there
carries one or more authority identifiers: GND (Deutsche
Nationalbibliothek), VIAF (international), and, where available,
Wikidata. This is the interoperability layer in the sense of FAIR:
any future tool that understands GND or VIAF URIs can integrate
this edition's data without parsing TEI.

#### 4b. Works and editions — `works-heiEDITIONS.xml`

Every `<title>` inside a `<bibl>` carries a `@ref` to an entry in
`works-heiEDITIONS.xml`. Different *abbreviation forms* used by Barth
in running text — *C. D. h.*, *ib.*, *Cur Deus homo*, sometimes just
a chapter number — all resolve to the **same** work ID
(`#cur-deus-homo`). This means that "find all places where Barth cites
*Cur Deus homo*" is one query, not a regex over typographic variants.

The Schmitt citation system (band/page/line) is preserved as
`<citedRange unit="schmitt">II 47,9</citedRange>`. The unit `schmitt`
is declared in `fqi-toc.xml#cu-schmitt` (taxonomy `citation-units`).
Every `<citedRange unit="schmitt">` in the edition carries
`@source="works-heiEDITIONS.xml#ed-schmitt"`, locating the citation in
the Schmitt critical edition as a concrete *manifestation* (in FRBR
terms) while `<title ref="…">` continues to identify the *work*. See
§ *Schmitt citations — nested form* below for the internal structure of
each `<citedRange unit="schmitt">`; see § *Extensibility — Editions
Layer* for how this hook is meant to scale (e.g. to a future Migne or
Internet-Archive link layer).

By placing the *band/page/line* tail inside `<citedRange unit="schmitt">`
rather than leaving `II 47,9` as inline text, the edition turns what is,
in print, an opaque alphanumeric string into a structured, queryable
citation object. A reverse index of the kind *"all footnotes in § 1
that cite Schmitt volume III"* becomes a single XPath
(`//citedRange[@unit='schmitt' and starts-with(., 'III')]`) rather
than a regex over typographic variants. Where the `unit="schmitt"`
element wraps three nested `<citedRange>` children (`unit="volume"`,
`unit="page"`, `unit="line"`; see § *Schmitt citations — nested form*
below), the same query gains line-level granularity:
`//citedRange[@unit='line']/@from` returns every line number cited in
the work. This is the concrete sense in which the digital edition
realises Šimek's *extensional* dimension (Šimek 2022, §§ 3.4, 4)
on the citation layer itself: the apparatus's bibliographic references
are no longer ornamental annotations of a base text but **first-class
data** that can be aggregated, counted, cross-referenced and resolved
against external manifestations — operations that print can present
but cannot perform.

##### Schmitt citations — nested form

Inside every `<citedRange unit="schmitt">`, the band/page/line triple is
not stored as plain text but as three nested `<citedRange>` children
whose `@unit` values name the parts:

```xml
<citedRange unit="schmitt" source="works-heiEDITIONS.xml#ed-schmitt">
  <citedRange unit="volume">II</citedRange>
  <citedRange unit="page">47</citedRange>,
  <citedRange unit="line" from="9">9</citedRange>
</citedRange>
```

The rendered text remains `II 47,9` — visible punctuation (the space
after the volume, the comma before the line) sits between children as
plain text and is preserved verbatim. The structure adds three things:

1. **Programmatic indexing.** `//citedRange[@unit='volume']/text()`
   yields every Schmitt volume cited in the edition; counting per
   volume becomes a trivial XPath.

2. **Numeric line anchors.** `@from` on `<citedRange unit="line">`
   carries the leading integer of the line specification, even where
   the textual form preserves the imprecise upper bound `f.` or `ff.`.
   A future viewer that aligns Schmitt page facsimiles to the edition
   can use `@from` as the seek target while still displaying `8f.` to
   the reader.

3. **Faithful representation of compound and range citations.**
   - `II 48,8f.; 49,19` — two `page,line` pairs separated by a literal
     `; ` between them. Both refer to volume II.
   - `I 83,27–84,2` — cross-page range, encoded with `@type="range"`
     on the outer `<citedRange unit="schmitt">` and two `page,line`
     pairs internally (the en-dash between them is plain text, kept
     for the reading view). No `@to` is emitted, because the range
     spans pages, not lines. The `@type="range"` marker means *"this
     is a continuous span"* is first-class data, not an inference
     from the en-dash glyph — XPath `//citedRange[@unit='schmitt'
     and @type='range']` returns every cross-page citation in the
     edition.
   - `II 116,5ff.` — single page/line, the `ff.` kept in the line
     element's text, `@from="5"`.

This is a deliberate trade-off. A purely attribute-based encoding
(`<citedRange unit="schmitt" vol="II" page="47" line="9"/>`) would be
denser, but it cannot represent cross-page ranges or `f./ff.`
imprecision without inventing custom attribute semantics. The nested
form keeps the legible text untouched and uses the element tree only
where the data are unambiguous.

#### 4c. Theological concepts — `terms-heiEDITIONS.xml`

This is an **extension** of Šimek's fourth dimension specific to
this edition. Theological texts are not merely *about* persons and
works; they are *constituted by* a technical vocabulary that mediates
between philosophical and dogmatic traditions.
`terms-heiEDITIONS.xml` makes this vocabulary explicit and queryable.

Entries fall into four categories (`@type`):

- `scholastic-latin` — Latin scholastic terms taken over by Barth
  from Anselm and the tradition: *intelligere*, *intellectus fidei*,
  *ratio*, *fides*, *veritas*, *rei veritas*, *probare*, *delectatio*,
  *pulchritudo*, *utilitas*, *Deus*. Single words use a
  lemma-concept model: all inflected forms (*intelligens*,
  *intelligendi*, *intelligatur*, …) are gathered under the lemma
  (`#intelligere`) via `<form type="inflected">`. Multi-word phrases
  with distinct semantic weight (*intellectus fidei*, *esse rei in
  intellectu*) are independent concept entries.

- `motto` — programmatic sentences with title-like force: *Fides
  quaerens intellectum*, *Credo ut intelligam*, *quaerere
  intellectum*, *unum argumentum*.

- `barthian` — German technical terms that Barth himself marks (most
  commonly via *Sperrsatz*) as terminologically loaded: *Notwendigkeit
  der Theologie*, *Wesen des Glaubens*, *Position*, *Negation*,
  *Nachdenken*.

- `dogmatic-shared` — concepts that belong neither specifically to
  Anselm nor specifically to Barth, but which are profiled here in
  a particular reading: *Gottesbeweis*, *ontologischer Gottesbeweis*,
  *Theologie*, *esse rei in intellectu / intelligere rem esse*.

Each entry carries bilingual `<gloss xml:lang="de">` and
`<gloss xml:lang="en">` definitions, GND identifiers where they
exist, notes on usage, and — where present — a list of occurrences
in the part files.

#### Two design rules for term annotation

**Validity rule.** A token is annotated as a `<term>` not because it
is Latin or letter-spaced, but because in the FQI context it carries
*theologically definitional* weight. *Aufgabe* on its own is not a
term; the concept it points to appears only inside the phrase
*Notwendigkeit der Theologie*. *Beweis* in everyday usage is not a
term; in the context of FQI it points to the dogmatic concept
*Gottesbeweis*. The `<term>` annotation reflects the second case,
not the first.

**Economy rule.** *Intelligere* occurs nine times in the running text
of § 1.1. To wrap every occurrence in a three-deep
`<term><foreign><hi>` nesting would inflate the XML without adding
information: every occurrence after the first one in a paragraph is
identifiable by its surrounding `<foreign xml:lang="la"><hi
rend="#italic">`. The convention adopted here is therefore: **the
first occurrence of a concept in each paragraph is annotated with
`<term ref="terms-heiEDITIONS.xml#…">`; subsequent occurrences in
the same paragraph are not.** This is an editorial decision, not a
philological omission, and is documented in the `<editorialDecl>`
inside `fqi-toc.xml`.

#### Forward-reference entries

`terms-heiEDITIONS.xml` includes entries for concepts whose *first
attested occurrence* lies beyond § 1 but which are part of the book's
core vocabulary: *Nachdenken* (the Barthian term that succeeds
*intelligere* in later sections), *ontologischer Gottesbeweis* (the
Kantian label that Barth wants to undercut, treated in chap. II), and
the half-prolepsis *unum argumentum* (which appears once in § 1 as
a foreshadowing citation but is terminologically unfolded only in
chap. II).

These entries have no `<term>` anchor inside the current encoded
scope. The viewer surfaces them through the term register, allowing
the user to see how the prototype's conceptual scaffolding is
designed to scale to the full book. The mechanism is documented as
follows:

- A forward-reference entry that has *no* attestation in the current
  scope carries `resp="forward-reference"` and contains no
  `<note type="occurrences">`.
- A forward-reference entry that has a *foreshadowing* occurrence
  (citation, name-drop) but whose *terminological* work happens
  later drops the `resp="forward-reference"` attribute and explains
  the half-proleptic status in its `<note type="usage">`. *Unum
  argumentum* is the example.
- A future validation script will check that the two states stay
  consistent: a pure forward-reference entry must have zero
  `<term ref>` pointers in the part files; a half-proleptic entry
  must have at least one.

### 5. Dimension of transmission plurality — apparatus criticus

The dimension of variant readings.

The edition has four witnesses (see `<listWit>` in `fqi-toc.xml`):
**A** (1st ed. 1931, München), **B** (pre-print of § 1.1 in the
Kattenbusch festschrift, ZThK NF 12, 1931, pp. 350–358), **A2**
(2nd ed. 1958, Zollikon-Zürich, the last edition revised by Barth
himself), and **GA** (the *Gesamtausgabe* text, here cited from
the 3rd ed. 2002).

Variants are encoded by **parallel segmentation** (TEI Guidelines
ch. 12.2.2):

```xml
<app xml:id="app-1-a" type="variant">
  <lem wit="#GA #A2 #A">Wir fragen zunächst nach</lem>
  <rdg wit="#B">Wir fragen nach</rdg>
</app>
```

Each inline `<app>` is *additionally* mirrored by a printbook-faithful
editorial note at the end of the chapter (`<note type="editorial"
target="#app-1-a">`). This **double encoding** serves two purposes:

1. The front-end synopsis (in `viewer_whole.html`) can collapse or
   expand the apparatus by toggling between display modes; the
   *Apparat* mode renders all `<app>` blocks as inline parallel
   readings, while the *GA Druckansicht* / *Lesefluss* modes show
   only the lemma and surface the variants via the editorial-note
   layer.
2. The editorial note layer preserves the *printbook-typographic*
   shape of the apparatus, in which letters `a, b, c …` anchor
   variant notes at the bottom of the chapter. This is the form
   a future print reissue or PDF derivative would need.

The apparatus convention follows the Sigel system established by
Jüngel and Dalferth in the GA II/13 *Vorwort* (1979).

### Witness-coverage logic in the viewer

A side effect of the four-witness encoding is that not every witness
is *present* on every page of the GA. **B** covers only GA pp. 13–20
(the Kattenbusch pre-print of § 1.1 and its immediate context);
attempting to display "B" view on GA p. 50 is meaningless. The
viewer reads the `<biblScope unit="ga-page" from="…" to="…">`
attached to each `<witness>` in the TOC and disables the B-button
whenever the currently displayed section falls outside B's coverage
range. This is computed at runtime against the part file's actual
`<pb>`-span, not against a hard-coded list — a small but real
illustration of why the witness declarations have to live in machine-
readable structure rather than in prose.

---

## Extensibility — Editions Layer

This section documents an **intentionally partial** extension of the
data model: hooks are in place for a full work / manifestation
separation à la FRBR, but the manifestation layer is populated by
exactly one example. The point is to make the architecture legible
to a reviewer without inflating the demonstrator beyond its scope.

### FRBR work-vs-manifestation in this edition

Anselm's *Proslogion* is, in FRBR vocabulary, a **work**. The Schmitt
critical edition (vol. I, 1946) and Migne PL 158 are **manifestations**
of that work. Barth's citation system in FQI operates on the Schmitt
manifestation (`I 100,18` = Schmitt vol. I, p. 100, l. 18) but
intends a reference to the work.

The data model reflects this:

- `works-heiEDITIONS.xml#proslogion` is a `<bibl>` describing the
  **work** *Proslogion*. It carries the GND ID, the author reference,
  and the work's bibliographic identity.
- *Nested inside* `<bibl xml:id="proslogion">` is a `<listBibl
  type="editions">` containing exactly one `<bibl xml:id="ed-schmitt"
  type="edition">`: this is the **manifestation layer** in
  miniature.
- In `fqi-part-I.xml` (and the mirror file
  `fqi-barth-1931-section1-heiEDITIONS.xml`), every `<citedRange
  unit="schmitt">` carries `@source="works-heiEDITIONS.xml#ed-schmitt"`.
  An attached XML comment on the first occurrence (`Prosl.` 1 = I 100,18,
  inside fn-10) flags this as the FRBR work/manifestation hook; the
  remaining occurrences carry the same `@source` silently. The hook is
  thus a working convention of the edition, not a one-off demo.

### Four extension hooks

The four mechanical hooks are:

1. **Editions list inside a work.** `<bibl xml:id="proslogion">`
   contains a `<listBibl type="editions">` with `<bibl
   xml:id="ed-schmitt" type="edition">`. Adding the Migne edition or
   a digital edition would be another sibling `<bibl>` inside the
   same `<listBibl>`, with its own `xml:id`. No structural change
   needed.
2. **`@source` on `<citedRange>`.** A `<citedRange>` whose unit
   matches a particular edition's citation system can carry
   `@source="works-heiEDITIONS.xml#ed-schmitt"`. The reader thereby
   learns that the citation `I 100,18` is *resolved against* the
   Schmitt manifestation, not against the work in general.
3. **Citation-unit taxonomy.** `fqi-toc.xml#citation-units` declares
   all `@unit` values currently in use (`chapter`, `schmitt`,
   `book-chapter`, `migne-pl`, `page`, `ga-page`). Adding a new
   citation system (e.g. `lambeth` for Lambeth-Library MS shelfmarks)
   would mean: declare a new `<category>` in this taxonomy, then use
   the corresponding `@unit` on `<citedRange>` and optionally link
   to an edition via `@source`.
4. **External cross-linking — roadmap only.** A future
   `anselm-werke.xml` could provide a parallel data file structured
   like `works-heiEDITIONS.xml` but oriented around Anselm's *opera*
   as a corpus. Cross-references from Barth's citations into a
   digital Anselm edition (e.g. a hypothetical Schmitt-digitised
   project, or the Anselm volume of *Patrologia Latina* in a
   web-accessible form) would be `@target` URIs on `<ref>` or
   `<bibl>` pointing into that file or into the external resource.

### What is intentionally out of scope

- **`<listPrefixDef>` for citation-system URI templates** (i.e. a
  mechanism that would map `schmitt:I/100/18` to a resolver URL).
  Without an actual digital Schmitt edition to resolve against, a
  `<listPrefixDef>` would be a label without a referent. It is
  better to leave the hook empty and visible than to claim a
  resolution that does not exist.
- **Full population of the editions list.** Migne PL 158, the
  *Opera Omnia* of Gerberon (1675), and the digital Bibliotheca
  Latina texts are all relevant editions of Anselm; the
  demonstrator does not enumerate them. The architecture supports
  them.
- **Cross-links from the FQI edition into a putative Anselm
  edition.** These would require the Anselm edition to exist. A
  hook is described above; no concrete `@target` URIs are emitted.

The pattern — *one example fully encoded, the architecture
documented* — is the same one Šimek 2022 § 4 describes for
heiEDITIONS prototypes: a small fully realised example is better
evidence of feasibility than a large half-realised one.

---

## BCP-47 translation extensibility

The edition is monolingual in its current form (German source text,
German editorial apparatus). A bilingual or polylingual extension
should be possible without restructuring.

The convention is:

- A translation file is named by inserting the **BCP-47 language tag**
  before the `.xml` extension: `fqi-part-I.zh-CN.xml` is the Mandarin
  translation of `fqi-part-I.xml`; `fqi-toc.en.xml` would be the
  English-language TOC.
- `xml:id` values are **stable across language versions**: the section
  with `xml:id="sec-I-1"` is "Die Notwendigkeit der Theologie" in
  German and (when the translation exists) "神学的必要性" in
  Mandarin, but it is *the same conceptual section*. URL-hash routing
  (`#sec-I-1`) therefore works identically across all languages.
- The viewer supports a query-string language hook: `viewer_whole.html?lang=zh-CN`.
  The hook attempts to fetch the matching `.zh-CN.xml` sibling
  file; if that file does not exist, it **silently falls back to the
  default-language file**. This means that the German edition
  continues to work even when only some files have been translated.
- `<langUsage>` in `fqi-toc.xml` declares all languages in use across
  the edition (currently `de` and `la`; `zh-CN` and `en` reserved as
  target-language placeholders).

No translation files currently exist; the mechanism is documented
as part of the scaffolding.

---

## FAIR data and the interchange / interoperability distinction

Following Martin Holmes, *Whatever happened to interchange?* (Digital
Scholarship in the Humanities 32, suppl. 1, 2017, pp. i63–i68), and
Šimek 2022 n. 14, this edition distinguishes between two ambitions
often conflated under "FAIR":

**Interoperability** — the property that *external* systems (a GND
viewer, a TEI-aware search index, a digital Anselm edition) can do
something useful with this edition's data *as it stands*. The TEI
markup, the GND-VIAF-Wikidata identifiers in
`persons-heiEDITIONS.xml` and `works-heiEDITIONS.xml`, the standard
TEI element names and attribute values, and the public XML namespaces
all support interoperability.

**Interchange** — the much harder property that a *different* TEI
project could absorb this edition's encoding decisions wholesale into
its own data model without information loss or model breakage. Holmes
argues, persuasively, that interchange in this strong sense is mostly
not achieved by TEI projects, and that the rhetoric of "TEI as a
shared standard" often overstates how much of the encoding is in
practice transferable.

This project does not claim to achieve full interchange with the
heiEDITIONS infrastructure (the heiEDITIONS frontend and database
layer are not publicly reusable). What it does claim is **interchange
quality** at the data level: the TEI files are structurally,
semantically, and documentarily prepared in such a way that they
could be absorbed into heiEDITIONS — or into a comparable editorial
infrastructure — with reasonable effort.

Concretely:

- **Findability (F)** through GND / VIAF / Wikidata identifiers and
  unique `xml:id`s.
- **Accessibility (A)** through plain TEI XML files in a public
  GitHub repository, no proprietary tooling required.
- **Interoperability (I)** in the sense outlined above.
- **Reusability (R)** through a documented CC BY-NC-SA 4.0 license
  on the encoding (the edited text remains under TVZ copyright; this
  is a portfolio prototype, not a publication).

What it does *not* claim:

- That its `<term>` annotations would survive transplantation into
  another theological edition project without remodeling. Theological
  vocabulary is too context-specific for that; another project's
  terms file would necessarily look different.
- That the four-witness apparatus convention scales beyond Barth's
  specific transmission history.
- That the lemma-concept granularity in `terms-heiEDITIONS.xml`
  (one entry per Latin lemma, with all inflected forms gathered
  under it) would be the right choice for an Anselm edition itself,
  which would want finer linguistic granularity.

In short: **this prototype is interoperable and interchange-ready
at the data level, but not "interchangeable" with heiEDITIONS as a
running infrastructure.** Acknowledging this matters more than
papering it over.

---

## How to view

The viewer is a static single-file HTML application. It uses `fetch()`
to load TEI XML files from the same directory; for security reasons
modern browsers block `fetch()` over the `file://` protocol. **The
viewer must therefore be opened from an HTTP server**, not by
double-clicking `viewer_whole.html` in a file browser.

The simplest local server, from the repository root:

```sh
python3 -m http.server 8000
```

Then open <http://localhost:8000/viewer_whole.html> in a browser.

For deployment via **GitHub Pages**: enable Pages on the repository
(`Settings → Pages → Source: main branch / root`), and the viewer
will be served at `https://<user>.github.io/<repo>/viewer_whole.html`
with the part files automatically resolved relative to it.

### Language hook

Append `?lang=` to the URL to request a translation, e.g.
`viewer_whole.html?lang=zh-CN`. Falls back to `de` if the requested
translation file does not exist. See § *BCP-47 translation
extensibility*.

### URL-hash routing

`viewer_whole.html#sec-I-1` opens the viewer on § 1 of Chapter I
directly. `#contents` returns to the TOC landing page. Any `xml:id`
on a `<div>` in any part file is a valid hash target; the viewer
resolves the hash via the TOC.

---

## Viewer architecture

`viewer_whole.html` is a single-file HTML application (~3500 lines
including comments). It has no build step and no runtime
dependencies other than a modern browser. Key design points:

- **No CETEIcean.** An earlier draft used the CETEIcean library
  (Cayless / Viglianti) to convert TEI to custom HTML elements
  (`<tei-p>`, `<tei-hi>`). The current version transforms TEI
  natively in the browser via a small recursive `convertNode`
  function. Reason: a single 30-line function gives us full control
  over which TEI attributes become HTML attributes (notably
  `xml:id` → `id`, which CETEIcean does not do by default and which
  we need for anchor-scroll routing); the size cost of the
  CDN-loaded CETEIcean library is therefore not justified.
- **Six display modes.** *GA · Druckansicht* renders the GA text as
  a sequence of `.ga-page` frames (page-width white panels with
  scroll-snap-proximity for sanft einrastendes vertikales Scrollen,
  no Page-Navigation-Buttons, no arrow-key page-flip). *GA ·
  Lesefluss* renders the same content as continuous text without
  page frames, the editor's footnotes pooled at the bottom of each
  section (mirroring the print apparatus style across A / A2 / B).
  *A · 1931*, *A2 · 1958*, and *B · Vorabdruck* render the
  corresponding witness texts (where present). *Apparat* renders
  all `<app>` blocks as inline parallel readings.
- **Collapsible TOC sidebar.** The 320 px left-hand TOC column can
  be toggled via a hamburger button (`☰`) placed on the toolbar
  alongside the font-size selector and theme switch. **Default state
  is collapsed**, both for desktop and for mobile. Re-opening the
  TOC restores the last-visited node as active.
- **Citation suggestion bar.** A persistent panel (anchored to the
  toolbar row, *not* to the scrolling main pane) shows Chicago 17
  and APA 7 citations for the active section. The bar is filled
  from `fqi-toc.xml` data; the section title is derived from the
  active TOC item, not from any `<title type="part">` in the part
  file (which we deliberately do not maintain — see § *Single
  source of truth for part-level metadata*).
- **Term hover.** Hovering a `<term>` or any `<rs ana="hc:…">` or
  similar entity reference fetches the data-layer entry on first
  hover, caches it in memory, and shows a tooltip with the
  bilingual gloss and (where present) the term type
  (`scholastic-latin` / `motto` / `barthian` / `dogmatic-shared`).
- **Aggregate fallback.** An `aggregate` TOC node renders the
  concatenation of its `kodiert` and `stub` children in page order.
  If the aggregate node has no fetchable children, the viewer
  degrades to a placeholder identical in UX to `nicht-kodiert`.
- **B-button gating.** The B view-mode button is automatically
  disabled when the current section falls outside B's GA-page
  coverage range, computed at runtime from the
  `<biblScope unit="ga-page">` attached to `<witness xml:id="B">`
  in `fqi-toc.xml`. (Currently § 1 sits inside B's range, so the
  button is always enabled; this will change when § 2 onward is
  encoded.)

---

## Technical conventions

### Encoding

- **TEI P5**, RELAX NG schema reference via `xml-model` processing
  instruction at the top of each XML file.
- All Latin material carries explicit `@xml:lang="la"`.
- Rendition classes (`#letter-spaced`, `#italic`) are declared in
  `<tagsDecl>` and target CSS in the viewer.
- IDs use lower-case ASCII with hyphens (`cur-deus-homo` not
  `CurDeusHomo`); IDs that include German names lose umlauts
  (`juengel`, `stoevesandt`).
- `xml:id` values for structural anchors follow stable patterns:
  `part-I`, `sec-I-1`, `sec-II-A-2`, `anhang-I`, `register-III`,
  etc. The `xml:id`s are stable across translation files.

### File interlinking

Cross-file references use the form `<targetfile>#<id>`, e.g.
`<persName ref="persons-heiEDITIONS.xml#anselm">` or
`<term ref="terms-heiEDITIONS.xml#intellectus-fidei">`. This is the
syntax expected by most TEI processors.

Within the viewer, the equivalent runtime form is a URL hash
(`viewer_whole.html#sec-I-1`); the viewer resolves the hash by
looking up the TOC and fetching the matching part file.

### Apparatus

Parallel-segmentation `<app>` with `<lem>` (text of the base witness,
here `#GA` ≡ `#A2`) and one or more `<rdg>` with `@wit` listing
divergent witnesses. The base witness is never explicitly cited;
only divergences from it. This follows the editorial declaration of
GA II/13 itself (see `<editorialDecl>` in `fqi-toc.xml`).

---

## Validation

Two layers of validation are run:

1. **XML well-formedness** with `xmllint --noout`. All current files
   parse cleanly.
2. **Project-internal reference consistency** (planned, to be added
   under `scripts/`). A Python script will check that:
   - every `<term ref="terms-heiEDITIONS.xml#…">` in any part file
     resolves to an `<entry xml:id="…">` in `terms-heiEDITIONS.xml`;
   - every `<persName ref="persons-heiEDITIONS.xml#…">` resolves to
     a `<person xml:id="…">` or `<org xml:id="…">` in
     `persons-heiEDITIONS.xml`;
   - every `<title ref="works-heiEDITIONS.xml#…">` resolves to a
     `<bibl xml:id="…">` in `works-heiEDITIONS.xml`;
   - every `<ref target="fqi-part-X.xml#…">` in `fqi-toc.xml`
     resolves to a real `xml:id` in the named part file, *or*
     carries a `todo:`-prefixed pseudo-URI consistent with its
     `<note type="encoding-status">nicht-kodiert</note>`;
   - forward-reference entries in `terms-heiEDITIONS.xml` are not,
     in fact, anchored in any part file (and conversely, that
     half-proleptic entries do have at least one anchor).

A future ODD customization will add Schematron rules that fold
these checks into schema validation itself.

---

## Roadmap

Listed roughly in dependency order. ✦ marks the next concrete step.

1. ✓ Minimal TEI template: full `teiHeader` and § 1 transcription
   demonstrating all five Šimek dimensions.
2. ✓ External `persons-heiEDITIONS.xml` and `works-heiEDITIONS.xml`
   with GND links.
3. ✓ External `terms-heiEDITIONS.xml` with bilingual glosses and
   forward-reference entries.
4. ✓ Multi-file architecture with central `fqi-toc.xml` (Šimek
   2022 § 3) and one fully encoded part file (`fqi-part-I.xml`).
5. ✓ Editions-layer hook in `works-heiEDITIONS.xml` documenting
   FRBR work-vs-manifestation separation.
6. ✓ Single-file viewer `viewer_whole.html` with six display modes,
   collapsible TOC, citation bar, term hover, language hook,
   URL-hash routing, witness-coverage gating.
7. ✓ README documenting the five-dimension structure, the multi-file
   architecture, the four-status system, and the FAIR / interchange
   distinction.
8. ✦ ODD customization, generation of project-specific RELAX NG
   and Schematron schemas.
9. Extension of encoding to a second section (likely § 2 or
   Anhang I) to exercise the cross-section routing.
10. Reference-consistency validation script under `scripts/`.
11. GitHub Pages deployment.
12. Polishing, portfolio documentation for applications.

---

## License

- **Encoding, modeling, and apparatus elaboration:** CC BY-NC-SA 4.0
  (see `LICENSE`, to be added).
- **Edited text:** © Theologischer Verlag Zürich (TVZ), Karl Barth
  Gesamtausgabe II/13 (1981, 3rd ed. 2002). The text is reproduced
  here under fair-use conventions for a non-public portfolio
  prototype, not as a publication. Any third-party use of the text
  itself requires permission from TVZ.
- **GND data** in `persons-heiEDITIONS.xml` and
  `works-heiEDITIONS.xml`: CC0 (Deutsche Nationalbibliothek).

---

## Contact and citation

Citation (provisional):

> [Bearbeiter:in der Promotion], *Fides quaerens intellectum: A
> Digital Edition Prototype*. Heidelberg, 2026. Available at:
> [URL of GitHub repository].

For questions about encoding decisions, see the `<editorialDecl>` in
`fqi-toc.xml`, which is the canonical place where editorial choices
are explained and motivated. Discrepancies between this README and
`<editorialDecl>` should be resolved in favor of `<editorialDecl>`.

---

## References

- Karl Barth, *Fides quaerens intellectum. Anselms Beweis der
  Existenz Gottes im Zusammenhang seines theologischen Programms.*
  Edited by Eberhard Jüngel and Ingolf U. Dalferth. Karl Barth
  Gesamtausgabe, II/13. 1st ed. 1981, 3rd ed. Zürich: TVZ, 2002.
- F. S. Schmitt (ed.), *S. Anselmi Cantuariensis archiepiscopi
  Opera Omnia.* 6 vols. Seckau / Rome / Edinburgh: Nelson,
  1938–1961.
- Martin Holmes, "Whatever happened to interchange?" *Digital
  Scholarship in the Humanities* 32, suppl. 1 (2017): i63–i68.
  doi: 10.1093/llc/fqx034.
- Jakub Šimek, "heiEDITIONS — eine Heidelberger Infrastruktur für
  Editionen (nicht nur) mittelalterlicher Texte." *Beiträge zur
  mediävistischen Erzählforschung. Themenheft* 12 (2022): 27–46.
- heiEDITIONS, *Konzepte.* <https://heieditions.github.io/concepts/concepts.html>.
- TEI Consortium, *TEI P5: Guidelines for Electronic Text Encoding
  and Interchange.* Version 4.x.
  <https://tei-c.org/release/doc/tei-p5-doc/en/html/>.
- IFLA, *Functional Requirements for Bibliographic Records (FRBR).*
  Munich: Saur, 1998; revised as IFLA LRM (2017). Cited for the
  work / expression / manifestation / item distinction underlying
  the editions-layer model.
