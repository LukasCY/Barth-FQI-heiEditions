# Fides quaerens intellectum — Digital Edition Prototype

A TEI P5 prototype edition of § 1 of Karl Barth's *Fides quaerens
intellectum* (Anselm monograph, 1931; cited from the *Gesamtausgabe*
II/13, 3rd ed. Zurich: TVZ, 2002), encoded for the
**heiEDITIONS** infrastructure model.

**Live demo:** <https://lukascy.github.io/Barth-FQI-heiEditions/viewer_whole.html>
&nbsp;·&nbsp;
**Full documentation:** [`ENCODING.md`](ENCODING.md)

---

## Why this text

Barth's 1931 Anselm monograph is the methodological hinge between
the dialectical and the dogmatic phases of his theology, and §  1
sets the program of the entire book. It is also, philologically,
exactly the kind of source on which a digital edition can show
what print cannot:

- **Three-language interleaving** — German prose, Latin scholastic
  terminology, Latin block-quotations from Anselm — that print
  reproduces typographically (Antiqua / italic / letter-spacing)
  but cannot make queryable.
- **A dense citation network** into Anselm's *Opera Omnia*, with
  Schmitt-edition references (`II 47,9` = vol. II, p. 47, l. 9)
  that print can annotate but not link.
- **A four-witness recension** (1st ed. 1931, 1931 pre-print of
  §  1.1, 2nd author-revised ed. 1958, *Gesamtausgabe* 2002)
  documented by the GA editors Jüngel and Dalferth — a non-trivial
  apparatus, not just a textual curiosity.
- **A theological vocabulary** in which the *same German word* is
  sometimes a technical term (when set in letter-spacing) and
  sometimes not. An apparatus entry that records the presence or
  absence of letter-spacing records a *semantic* difference, not
  merely a typographic one.

The portfolio bet is that solving these problems well on a
theologically demanding source generalizes more strongly than
solving them on a tractable one.

---

## What this prototype demonstrates

Three competences expected of a digital-humanities edition project:

1. **TEI P5 modeling for a multilingual scholastic-dogmatic text.**
   Four-witness apparatus encoded via **parallel segmentation**
   rather than location-referenced apparatus, because letter-spacing
   variants are semantic and need to live *in the text*, not in a
   detached note layer. Every Latin token carries `@xml:lang`. A
   four-category theological terms register
   (`scholastic-latin` / `motto` / `barthian` /
   `dogmatic-shared`) with bilingual glosses and GND identifiers
   makes Barth's distinct vocabularies queryable as such.
   Bibliographic entries follow the FRBR
   *work / expression / manifestation* distinction: a citation to
   *Proslogion* references the work; the Schmitt critical edition
   is modeled as a separable manifestation layer.

2. **heiEDITIONS-conformant multi-file architecture.** A central
   `fqi-toc.xml` carries project-wide metadata (witness list,
   editorial declaration, citation-unit taxonomy) and the full
   monograph's table of contents in its real structural shape.
   Part files are fetched on demand. A four-status system
   (`kodiert` / `stub` / `nicht-kodiert` / `aggregate`)
   makes the encoded scope visible *in context* rather than
   isolated as a free-floating fragment. BCP-47 sibling-file naming
   (`fqi-part-I.zh-CN.xml`, …) supports future translations without
   restructuring; `xml:id` values are stable across languages.

3. **A working viewer with no build step.** Single-file
   `viewer_whole.html`, vanilla JS, no framework, no CETEIcean
   dependency — the recursive `convertNode` function gives full
   control over the TEI-to-HTML attribute mapping (notably
   `xml:id` → `id` for anchor-scroll routing), which the
   CDN-loaded library does not do by default. Six display modes
   (GA Druckansicht / GA Lesefluss / A · 1931 / A2 · 1958 /
   B · Vorabdruck / Apparat); collapsible TOC sidebar; live
   Chicago-17 / APA-7 citation bar; term-hover tooltips;
   witness-coverage gating computed at runtime from
   `<biblScope unit="ga-page">`; URL-hash routing; `?lang=` query
   hook with silent fallback.

Methodological framework: Jakub Šimek, *heiEDITIONS — eine
Heidelberger Infrastruktur für Editionen (nicht nur)
mittelalterlicher Texte* (BmE Themenheft 12, 2022, pp. 27–46),
and the interoperability-vs-interchange distinction of Martin
Holmes, *Whatever happened to interchange?* (DSH 32, 2017,
pp. i63–i68).

---

## Design principles

Four operational rules adopted across the project, documented in
full with rationale and examples in
[`ENCODING.md`](ENCODING.md):

- **Hermeneutic discipline.** When transcribing TEI from a printed
  source, no framing labels ("Kapitel", "Abschnitt", running
  headers, structural Latin tags) are introduced that the source
  itself does not print. If such a label appears in an earlier
  draft, it is a bug to be reverted, not evidence of the source's
  wording.
  [→ details](ENCODING.md#hermeneutic-discipline)
- **Scaffold, don't skip.** The TOC encodes the *entire* monograph's
  structure; sections not yet transcribed are present in the TOC
  with a different status, not absent. This commits the project to
  fixed file-naming and `xml:id` conventions before encoding the
  rest, and shows the encoded fragment in its real structural place.
  [→ details](ENCODING.md#the-four-status-system)
- **Single source of truth for part-level metadata.** Part files do
  not carry `<title type="part">` strings that duplicate the TOC.
  Citation suggestions and navigational labels are derived from the
  active TOC entry. The TOC is the only authoritative source for
  structural labels.
  [→ details](ENCODING.md#single-source-of-truth-for-part-level-metadata)
- **Interchange quality, not full interoperability.** Following
  Holmes 2017, the project distinguishes between (i) machine
  interoperability via GND / VIAF / Wikidata identifiers and
  standard TEI (claimed) and (ii) absorption into the running
  heiEDITIONS infrastructure (not claimed; frontend and database
  layer are not publicly reusable). What *is* claimed is interchange
  quality at the data level: the TEI files are structurally and
  documentarily prepared so that they could be absorbed into
  heiEDITIONS or a comparable infrastructure with reasonable effort.
  [→ details](ENCODING.md#fair-data-and-the-interchange--interoperability-distinction)

---

## How the TEI is shaped (and why these specific choices)

The TEI encoding is not "TEI in general" but a set of named decisions, each
trading something away for something gained. Six of them are worth pointing
out, because they show up everywhere in the files and they are what makes
this prototype something different from a typographic transcription with
angle brackets.

1. **Šimek's five dimensions as the actual encoding scaffold, not as
   decoration.** Every `<editorialDecl>` declares the project's five
   working layers — *semantic-logical*, *visual-material*, *linguistic*,
   *extensional*, *variant* (Šimek 2022) — and the encoding of each
   `<note>`, `<bibl>`, `<persName>`, `<hi>` etc. is consistent with
   exactly one of them. The five-fold structure of `ENCODING.md` mirrors
   this. The cost is editor discipline; the gain is that asking
   *"what does this edition do at the extensional layer?"* is a single
   document section, not an archaeology dig.

2. **`@ana="hc:..."` on every semantic entity.** Names, work-titles,
   subject references and quotations carry an `@ana` value drawn from
   the heiEDITIONS concept vocabulary — `hc:PersonReference`,
   `hc:WorkReference`, `hc:SubjectReference`, `hc:PrimarySourceQuotation`,
   `hc:AuthorialNote`. This makes the apparatus *queryable by role*:
   `//persName[@ana='hc:PersonReference']` returns every person mention
   in the edition without traversing prose. heiEDITIONS-interchange is
   built on this: not just *that* an entity is referenced, but *as what*.

3. **Citation tails as structured data, not strings.** Where Barth writes
   `II 47,9` (Schmitt's *Opera Omnia*, volume II, page 47, line 9), the
   apparatus encodes this as a `<citedRange unit="schmitt">` whose three
   nested children (`unit="volume"`, `unit="page"`, `unit="line"`)
   expose the parts to XPath. `@from` on `<citedRange unit="line">`
   carries the leading integer even where the textual form preserves
   `f.` or `ff.`; `@source="works-heiEDITIONS.xml#ed-schmitt"` on the
   outer element anchors the citation in a specific FRBR-*manifestation*
   while `<title @ref>` continues to identify the FRBR-*work*. A reverse
   index *"all footnotes citing Schmitt vol. III"* becomes one XPath;
   *"all line numbers Barth cites from p. 47"* another.

4. **External authority files instead of inline strings.** Persons,
   works and theological terms each live in their own data file
   (`persons-heiEDITIONS.xml`, `works-heiEDITIONS.xml`,
   `terms-heiEDITIONS.xml`) with stable `xml:id`s and external authority
   links (GND, VIAF, Wikidata where applicable). The encoded text never
   says `<persName>Anselm</persName>` and is done with it; it says
   `<persName ref="persons-heiEDITIONS.xml#anselm">Anselm</persName>`.
   The cost is one extra indirection per entity; the gain is that
   correcting a date, adding an alternative spelling, or hooking the
   edition into a future linked-data context is a one-place change.

5. **Witness sigla declared once, attached everywhere.** The four
   witnesses (`#A`, `#A2`, `#GA`, `#B`) are declared in the project
   header. Every `<note>`, every editorial intervention, and every
   apparatus entry carries `@wit` listing the witnesses to which it
   applies. The variant layer is therefore not a separate document but
   a property of the same elements that carry the text — which is what
   makes parallel-segmentation visible to the reader-mode toggle in the
   viewer.

6. **Interchange, not interoperability — and the difference is
   load-bearing.** Following Holmes 2017 and Šimek's footnote 14, the
   project does not claim that the TEI plugs into the running
   heiEDITIONS frontend without work; the frontend and database layer
   of heiEDITIONS are not publicly reusable. What it does claim is that
   the data layer — the TEI, the personography, the bibliography, the
   term register, the rendering taxonomy — is structurally and
   documentarily prepared so that *importing* into heiEDITIONS or a
   comparable infrastructure is a tractable task, not a rewrite. The
   distinction is the difference between *"works out of the box"* and
   *"reads cleanly into the next system"*; this edition aims for the
   second.

The full element-by-element rationale, with examples and trade-off
discussion for each of these decisions, is in
[`ENCODING.md`](ENCODING.md).

---

## Repository layout

| Layer | File(s) |
| --- | --- |
| TOC + project-wide metadata | `fqi-toc.xml` |
| Content (one fully encoded part, six scaffolded) | `fqi-part-I.xml` &nbsp;·&nbsp; `_template_stub.xml` |
| Data layer | `persons-heiEDITIONS.xml` &nbsp;·&nbsp; `works-heiEDITIONS.xml` &nbsp;·&nbsp; `terms-heiEDITIONS.xml` |
| Viewer | `viewer_whole.html` |
| Documentation | `README.md` (this file) &nbsp;·&nbsp; `ENCODING.md` |

Each file's role and its place in the four-status TOC is documented
in [`ENCODING.md` § Repository structure](ENCODING.md#repository-structure).
Naming conventions (prefix `fqi-`, suffix `-heiEDITIONS`, BCP-47
language tags) are explained in
[§ File-naming convention](ENCODING.md#file-naming-convention).

---

## How to view

The viewer is a single-file static HTML application. Because it
uses `fetch()` to load TEI XML files, modern browsers block it
under `file://`; serve over HTTP instead. From the repository root:

```sh
python3 -m http.server 8000
```

Then open <http://localhost:8000/viewer_whole.html>.

For GitHub Pages: enable Pages on the repository, and the viewer is
served at `https://<user>.github.io/<repo>/viewer_whole.html`.

Language hook: append `?lang=zh-CN` (or any BCP-47 tag) to request
a translation; silent fallback to the German source if the
translation sibling file does not exist. See
[§ How to view](ENCODING.md#how-to-view) for details.

---

## Scope and status

| | |
| --- | --- |
| Encoded text | § 1 *Die Notwendigkeit der Theologie* (GA II.13, pp. 13–19) |
| TOC scaffolding | Complete monograph (2 chapters, frontmatter, *Anhang*, *Register*) |
| Apparatus | Four witnesses, parallel segmentation, all variants within scope |
| Term register | 50+ entries with GND links, bilingual glosses (DE/EN), forward-reference mechanism |
| Persons / works | Full GND / VIAF / Wikidata authority links for entities in scope |
| Viewer modes | 6 (GA Druckansicht, GA Lesefluss, A · 1931, A2 · 1958, B · Vorabdruck, Apparat) |

For the full roadmap (ODD customization, additional sections,
validation script, deployment) see
[§ Roadmap](ENCODING.md#roadmap).

---

## License

- **Encoding, modeling, and apparatus elaboration:** CC BY-NC-SA 4.0
  (`LICENSE`, to be added).
- **Edited text:** © Theologischer Verlag Zürich (TVZ), *Karl Barth
  Gesamtausgabe* II/13 (1981, 3rd ed. 2002). Reproduced here under
  fair-use conventions for a non-public portfolio prototype, not as
  a publication.
- **GND data:** CC0 (Deutsche Nationalbibliothek).

---

## Citation

> [Bearbeiter:in der Promotion], *Fides quaerens intellectum: A
> Digital Edition Prototype*. Heidelberg, 2026. Available at:
> [URL of GitHub repository].

For the canonical record of editorial choices see the
`<editorialDecl>` in `fqi-toc.xml` and the full method
documentation in [`ENCODING.md`](ENCODING.md).

---

## References

- Karl Barth, *Fides quaerens intellectum*. Edited by E. Jüngel and
  I. U. Dalferth. Karl Barth Gesamtausgabe II/13. 3rd ed., Zurich:
  TVZ, 2002.
- Martin Holmes, "Whatever happened to interchange?" *Digital
  Scholarship in the Humanities* 32, suppl. 1 (2017): i63–i68.
- Jakub Šimek, "heiEDITIONS — eine Heidelberger Infrastruktur für
  Editionen (nicht nur) mittelalterlicher Texte." *Beiträge zur
  mediävistischen Erzählforschung. Themenheft* 12 (2022): 27–46.
- heiEDITIONS, *Konzepte*. <https://heieditions.github.io/concepts/concepts.html>.
- TEI Consortium, *TEI P5: Guidelines for Electronic Text Encoding
  and Interchange*. <https://tei-c.org/release/doc/tei-p5-doc/en/html/>.
