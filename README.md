# Fides quaerens intellectum — Digital Edition Prototype

A TEI P5 prototype edition of § 1 of Karl Barth's *Fides quaerens
intellectum* (Anselm monograph, 1931; cited from the *Gesamtausgabe*
II/13, 3rd ed. Zurich: TVZ, 2002), encoded for the
**heiEDITIONS** infrastructure model.

**Live demo:** <https://lukascy.github.io/Barth-FQI-heiEditions/viewer_whole.html>
&nbsp;·&nbsp;
**Full documentation:** [`docs/ENCODING.md`](docs/ENCODING.md)

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
[`docs/ENCODING.md`](docs/ENCODING.md):

- **Hermeneutic discipline.** When transcribing TEI from a printed
  source, no framing labels ("Kapitel", "Abschnitt", running
  headers, structural Latin tags) are introduced that the source
  itself does not print. If such a label appears in an earlier
  draft, it is a bug to be reverted, not evidence of the source's
  wording.
  [→ details](docs/ENCODING.md#hermeneutic-discipline)
- **Scaffold, don't skip.** The TOC encodes the *entire* monograph's
  structure; sections not yet transcribed are present in the TOC
  with a different status, not absent. This commits the project to
  fixed file-naming and `xml:id` conventions before encoding the
  rest, and shows the encoded fragment in its real structural place.
  [→ details](docs/ENCODING.md#the-four-status-system)
- **Single source of truth for part-level metadata.** Part files do
  not carry `<title type="part">` strings that duplicate the TOC.
  Citation suggestions and navigational labels are derived from the
  active TOC entry. The TOC is the only authoritative source for
  structural labels.
  [→ details](docs/ENCODING.md#single-source-of-truth-for-part-level-metadata)
- **Interchange quality, not full interoperability.** Following
  Holmes 2017, the project distinguishes between (i) machine
  interoperability via GND / VIAF / Wikidata identifiers and
  standard TEI (claimed) and (ii) absorption into the running
  heiEDITIONS infrastructure (not claimed; frontend and database
  layer are not publicly reusable). What *is* claimed is interchange
  quality at the data level: the TEI files are structurally and
  documentarily prepared so that they could be absorbed into
  heiEDITIONS or a comparable infrastructure with reasonable effort.
  [→ details](docs/ENCODING.md#fair-data-and-the-interchange--interoperability-distinction)

---

## Repository layout

| Layer | File(s) |
| --- | --- |
| TOC + project-wide metadata | `fqi-toc.xml` |
| Content (one fully encoded part, six scaffolded) | `fqi-part-I.xml` &nbsp;·&nbsp; `_template_stub.xml` |
| Data layer | `persons-heiEDITIONS.xml` &nbsp;·&nbsp; `works-heiEDITIONS.xml` &nbsp;·&nbsp; `terms-heiEDITIONS.xml` |
| Viewer | `viewer_whole.html` |
| Documentation | `README.md` (this file) &nbsp;·&nbsp; `docs/ENCODING.md` |

Each file's role and its place in the four-status TOC is documented
in [`docs/ENCODING.md` § Repository structure](docs/ENCODING.md#repository-structure).
Naming conventions (prefix `fqi-`, suffix `-heiEDITIONS`, BCP-47
language tags) are explained in
[§ File-naming convention](docs/ENCODING.md#file-naming-convention).

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
[§ How to view](docs/ENCODING.md#how-to-view) for details.

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
[§ Roadmap](docs/ENCODING.md#roadmap).

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
documentation in [`docs/ENCODING.md`](docs/ENCODING.md).

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
