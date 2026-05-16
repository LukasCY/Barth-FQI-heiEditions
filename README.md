# Fides quaerens intellectum — A Digital Edition Prototype (heiEDITIONS-Bound Variant)

A TEI P5 prototype edition of § 1.1 ("Die Notwendigkeit der Theologie")
of Karl Barth, *Fides quaerens intellectum* (FQI), as published in volume
II/13 of the *Karl Barth Gesamtausgabe* (3rd ed., Zürich: TVZ, 2002).

This is a **portfolio project**, not a finished critical edition. Its
purpose is to demonstrate digital-philological method on a theologically
demanding text and to make every encoding decision explicit enough to be
inspected, critiqued, and extended.

This variant of the prototype binds the TEI encoding to **heiEDITIONS
Concepts**, the OWL class ontology maintained by Heidelberg University
Library. heiEDITIONS Concepts publishes LOD URIs for editorial concepts
(`SubjectReference`, `PrimarySourceQuotation`, `AuthorialNote`,
`VariantNote`, `IndexOfPersons`, …) and prescribes, per class, which TEI
elements may carry them via `@ana`. Binding to heiEDITIONS lifts the
edition from local TEI conventions into a controlled and published
vocabulary; it also makes the encoding directly compatible with the
Heidelberg DH infrastructure to which the project's target institutions
(Akademien, UB-Editionsdienste) increasingly converge.

The non-heiEDITIONS variant of the same data (without `xmlns:hc` and
without `@ana` annotations) is preserved in `fqi-barth-1931-section1.xml`
etc.; this README describes the **heiEDITIONS variant**, which lives in
the `-heiEDITIONS.xml` siblings.

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

The demonstrator covers four pages of the printed *Gesamtausgabe*
(GA pp. 13–16) and reproduces all editorial apparatus entries that
fall within this range.

---

## Repository structure

| File                                          | Purpose                                          |
| --------------------------------------------- | ------------------------------------------------ |
| `fqi-barth-1931-section1-heiEDITIONS.xml`     | Edited text: `teiHeader` + transcription of § 1.1 with all apparatus, footnotes, term annotations, plus heiEDITIONS `@ana` bindings. |
| `terms-heiEDITIONS.xml`                       | Theological-conceptual register (Latin scholastic terms, Anselmian *mottos*, Barthian German terms, shared dogmatic vocabulary); bound as `IndexOfSubjects`. |
| `persons-heiEDITIONS.xml`                     | Authority file for persons and corporate bodies; bound as `IndexOfPersons` (with a nested `IndexOfOrganizations` div for the `<listOrg>` block). |
| `works-heiEDITIONS.xml`                       | Bibliography of Anselm's works, reference editions, methodological literature; bound as `IndexOfWorks`. |
| `fqi-barth-1931-section1.xml` etc.            | Pre-heiEDITIONS variants of the same files, retained for comparison and to document the migration path. |
| `README.md`                                   | This file.                                       |
| `LICENSE` (to come)                           | CC BY-NC-SA 4.0 for encoding decisions and apparatus; the edited text itself remains under TVZ copyright. |
| `schema/` (to come)                           | ODD customization and generated RELAX NG / Schematron schemas. |
| `viewer/` (to come)                           | CETEIcean configuration and CSS / JS for the reading/source-view toggle. |

---

## Methodological framework: five textual dimensions

The encoding follows the five textual dimensions proposed by Jakub Šimek
in *heiEDITIONS — eine Heidelberger Infrastruktur für Editionen (nicht
nur) mittelalterlicher Texte* (BmE Themenheft 12, 2022, pp. 27–46).
Šimek's framework is well suited to a portfolio project because each
dimension corresponds to a clearly delimited set of TEI elements and to
a clearly delimited set of user questions a future edition needs to be
able to answer.

In the heiEDITIONS-bound variant, each dimension is *additionally*
anchored in the heiEDITIONS Concepts ontology where the ontology
provides an applicable class. The result is a layered semantics: TEI
elements carry their usual syntactic role, while `@ana` attributes
carry the editorial-conceptual role made queryable across projects.

### 1. Semantic–logical dimension

The dimension of *Sinneinheiten*: chapter, paragraph, sentence, the
hierarchy of meaning that the printed page encodes but does not name.

- `<div type="chapter" n="1">` for § 1
- `<head><num/><rs ana="hc:SubjectReference" ref="terms.xml#…"><hi rend="#letter-spaced">…</hi></rs></head>` for the chapter title
- `<p xml:id="p-13-1">` for paragraphs, with stable IDs derived from
  the page on which the paragraph begins.

heiEDITIONS also defines `Chapter`, `Paragraph`, `Heading` and similar
concepts; in this MVP they are not separately bound because the TEI
elements `<div type="chapter">`, `<p>`, `<head>` already carry the
relevant semantics, and an additional `@ana` would be redundant. A
future ODD customization may add them if the front-end or LOD export
benefits.

### 2. Visual–material dimension

The dimension of the *Textträger*: pages, layout regions, lines,
typographic distinctions.

- `<pb n="13" ed="#GA" xml:id="ga-13"/>` for page breaks. Two page
  numbering systems run in parallel: `ed="#GA"` for the *Gesamtausgabe*
  page, `ed="#A"` for the first-edition page.
- `<hi rend="#letter-spaced">` for *Sperrsatz* — the printer's device
  by which Barth marks emphasis and by which the apparatus tracks
  variants such as `app-1-d` (where the *Sperrsatz* on *Theologie* is
  present in GA / A2 but absent in A / B).
- `<hi rend="#italic">` for italic (Barth's signal for Latin
  block-quotation and for scholastic terms in running text).
- `<foreign xml:lang="la">` wraps Latin material linguistically (see
  dimension 3).

The `<tagsDecl>` declares CSS-targetable rendition classes
(`#letter-spaced`, `#italic`). heiEDITIONS has a corresponding class
`EditorialEmphasis` for emphasis introduced *by the editor*; it is not
applied to the printed *Sperrsatz* of the text itself, which is
*authorial* emphasis preserved as `<hi>` faithfulness.

### 3. Linguistic dimension

The dimension of *Sprachen* and (eventually) lemmatized annotation.

- `<language ident="de">` and `<language ident="la">` declared in
  `langUsage`.
- Every Latin word, phrase, or block-quote carries explicit
  `@xml:lang="la"` on `<foreign>`, `<quote>`, `<title>`, or `<rs>`.
- A future ODD customization will optionally allow `@lemma` and
  `@pos` on `<w>` for finer linguistic analysis; the demonstrator
  does not require it.

heiEDITIONS does not currently publish concepts for linguistic
annotation at this level, so no `@ana` binding is added here.

### 4. Extensional dimension — extra-referentiality

The dimension of references to entities outside the text: persons,
works, biblical passages, theological concepts. **This is the
dimension in which the prototype goes farthest beyond a print edition,
and where heiEDITIONS bindings carry most of their weight.**

#### 4a. Persons and corporate bodies — `persons-heiEDITIONS.xml`

In the running text, every `<persName>` carries a `@ref` pointing to
an `xml:id` in `persons-heiEDITIONS.xml`, and additionally
`ana="hc:PersonReference"`. The register file itself is declared
`<text ana="hc:IndexOfPersons">`, with a nested
`<div ana="hc:IndexOfOrganizations">` for the `<listOrg>` block.

Inside each `<person>`, the primary `<persName>` is annotated
`ana="hc:PreferredAppellation"`; alternative names (Latin form,
older form, abbreviated form) carry `ana="hc:AlternativeAppellation"`.
The same applies to `<orgName>` in `<listOrg>`.

GND, VIAF, and Wikidata identifiers are encoded as `<idno>`. The
GND-typed identifiers additionally carry `ana="hc:GNDURI"`. This is
the interoperability layer in the sense of FAIR: any future tool that
understands GND URIs can integrate this edition's data without parsing
TEI.

#### 4b. Works and editions — `works-heiEDITIONS.xml`

In the running text, every `<title>` inside a `<bibl>` carries a
`@ref` to an entry in `works-heiEDITIONS.xml` and additionally
`ana="hc:WorkReference"`. Different abbreviation forms used by Barth
— *C. D. h.*, *ib.*, *Cur Deus homo*, sometimes just a chapter number
— all resolve to the **same** work ID (`#cur-deus-homo`). "Find all
places where Barth cites *Cur Deus homo*" is therefore one query, not
a regex over typographic variants.

Inside `works-heiEDITIONS.xml` (which is declared
`<text ana="hc:IndexOfWorks">`), each `<bibl>` carries:

- one `<title type="full" ana="hc:PreferredAppellation">` for the
  canonical title used in the index;
- zero or more `<title type="alternative" | "abbr" | …
  ana="hc:AlternativeTitle">` for the variant forms.

The Schmitt citation system (band/page/line) is preserved as
`<citedRange unit="schmitt">II 47,9</citedRange>`, allowing future
deep linking to a digital Schmitt edition if and when one becomes
available.

#### 4c. Theological concepts — `terms-heiEDITIONS.xml`

This is an **extension** of Šimek's fourth dimension specific to this
edition. Theological texts are not merely *about* persons and works;
they are *constituted by* a technical vocabulary that mediates between
philosophical and dogmatic traditions. `terms-heiEDITIONS.xml` makes
this vocabulary explicit and queryable; it is declared
`<text ana="hc:IndexOfSubjects">`.

Entries fall into four categories (`@type`):

- `scholastic-latin` — Latin scholastic terms taken over by Barth from
  Anselm and the tradition: *intelligere*, *intellectus fidei*,
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
  Anselm nor specifically to Barth, but which are profiled here in a
  particular reading: *Gottesbeweis*, *ontologischer Gottesbeweis*,
  *Theologie*, *esse rei in intellectu / intelligere rem esse*.

Each entry carries bilingual `<gloss xml:lang="de">` and
`<gloss xml:lang="en">` definitions, GND identifiers where they exist
(as `<ref target="…gnd…" type="gnd" ana="hc:GNDURI">`), notes on
usage, and — where present — a list of occurrences in
`section1-heiEDITIONS.xml`.

In the running text, every theological reference is encoded as
`<rs ana="hc:SubjectReference" ref="terms-heiEDITIONS.xml#…">`
wrapping `<foreign>`, `<hi>`, or both. This replaces the TEI `<term>`
used in earlier drafts; the rationale is documented in **Encoding
Decision E1** below.

##### Two design rules for term annotation

**Validity rule.** A token is annotated as a `<rs ana="hc:Subject\
Reference">` not because it is Latin or letter-spaced, but because in
the FQI context it carries *theologically definitional* weight.
*Aufgabe* on its own is not a term; the concept it points to appears
only inside the phrase *Notwendigkeit der Theologie*. *Beweis* in
everyday usage is not a term; in the context of FQI it points to the
dogmatic concept *Gottesbeweis*. The annotation reflects the second
case, not the first.

**Economy rule.** *Intelligere* occurs nine times in the running text
of § 1.1. To wrap every occurrence in a three-deep
`<rs><foreign><hi>` nesting would inflate the XML without adding
information: every occurrence after the first one in a paragraph is
identifiable by its surrounding `<foreign xml:lang="la"><hi
rend="#italic">`. The convention adopted here is therefore: **the
first occurrence of a concept in each paragraph is annotated; subsequent
occurrences in the same paragraph are not.** This is an editorial
decision, not a philological omission, and is documented in the
`<editorialDecl>` of `section1-heiEDITIONS.xml`.

##### Forward-reference entries

`terms-heiEDITIONS.xml` includes entries for concepts whose *first
attested occurrence* lies beyond § 1.1 but which are part of the
book's core vocabulary: *Nachdenken* (the Barthian term that succeeds
*intelligere* in later sections), *ontologischer Gottesbeweis* (the
Kantian label that Barth wants to undercut, treated in § 5–7), and
the half-prolepsis *unum argumentum* (which appears once in § 1.1 as a
foreshadowing citation but is terminologically unfolded only in
§ 5–7).

These entries have no `<rs>` anchor inside `section1-heiEDITIONS.xml`
for the demonstrator. A future front end (see § 6 below) will surface
them through the term-register index. The mechanism:

- A forward-reference entry that has *no* attestation in the current
  scope carries `resp="forward-reference"` and contains no
  `<note type="occurrences">`.
- A half-proleptic entry (one foreshadowing occurrence; full
  terminological work later) drops the `resp` attribute and explains
  the status in its `<note type="usage">`. *Unum argumentum* is the
  example.
- The validation script (see § "Validation" below) checks that the two
  states stay consistent.

### 5. Dimension of transmission plurality — apparatus criticus

The dimension of variant readings.

The edition has four witnesses (see `<listWit>` in
`section1-heiEDITIONS.xml`): **A** (1st ed. 1931, München), **B**
(pre-print of § 1.1 in the Kattenbusch festschrift, ZThK NF 12, 1931,
pp. 350–358), **A2** (2nd ed. 1958, Zollikon-Zürich, the last edition
revised by Barth himself), and **GA** (the *Gesamtausgabe* text, here
cited from the 3rd ed. 2002).

Variants are encoded by **parallel segmentation** (TEI Guidelines
ch. 12.2.2):

```xml
<app xml:id="app-1-a" type="variant">
  <lem wit="#GA #A2 #A">Wir fragen zunächst nach</lem>
  <rdg wit="#B">Wir fragen nach</rdg>
</app>
```

Each inline `<app>` is *additionally* mirrored by a printbook-faithful
editorial note at the end of the chapter:

```xml
<note place="foot" n="a" xml:id="ed-a"
      type="editorial" ana="hc:VariantNote"
      target="#app-1-a">
  …
</note>
```

The `ana="hc:VariantNote"` binding places these notes in the
heiEDITIONS class hierarchy `VariantNote → TextCriticalNote →
EditorialNote → Note`. Barth's own footnotes are correspondingly bound
`ana="hc:AuthorialNote"`. Latin block-quotations from Anselm carry
`ana="hc:PrimarySourceQuotation"` on the `<quote>` element. The
**double encoding** (inline `<app>` + standoff editorial note) serves
two purposes:

1. The front-end synopsis (planned in CETEIcean) can collapse or
   expand the apparatus by toggling visibility of the inline `<app>`
   children.
2. The editorial-note layer preserves the *printbook-typographic*
   shape of the apparatus, in which letters `a, b, c …` anchor variant
   notes at the bottom of the chapter. This is the form a future print
   reissue or PDF derivative would need.

The apparatus convention follows the Sigel system established by
Jüngel and Dalferth in the GA II/13 *Vorwort* (1979).

---

## Encoding decisions

This section is the project's editorial-philological **conscience**.
It records the five non-trivial encoding choices that diverge from
either the default TEI conventions or the heiEDITIONS-default
prescriptions, and explains why. Each decision is identified by a code
(E1–E5) for stable cross-reference from the `<editorialDecl>` of the
XML files.

### E1. Theological references use `<rs ana="hc:SubjectReference">`, not `<term>`

**Earlier drafts** wrapped theological references in `<term ref="…">`,
in keeping with a common TEI idiom for terminology.

**The heiEDITIONS variant** uses
`<rs ref="terms-heiEDITIONS.xml#…" ana="hc:SubjectReference">`.

**Reasons:**

1. heiEDITIONS Concepts binds `SubjectReference` strictly to `<rs>`
   (and `<milestone>` in source-document encoding, irrelevant here).
   The class definition — "Verweis auf eine Sache, typischerweise ein
   Konzept, ein Thema oder einen Begriff, mit einer möglichen
   Verwendung für die Aufnahme ins Sachregister" — is exactly what
   these markers do.
2. TEI usage notes are themselves ambivalent on `<term>` vs `<rs>` for
   *references to* terminology: `<term>` works well for the *defining*
   occurrence (typically in a glossary or term register), while `<rs>`
   is the natural choice for *referential* mention in running text.
   The earlier `<term ref="…">` conflated the two roles. Splitting
   them — `<entry>` inside `terms-heiEDITIONS.xml` for the definition,
   `<rs>` in `section1-heiEDITIONS.xml` for the reference — is
   philologically cleaner and aligns with heiEDITIONS.
3. Inside `<keywords>` of the `<textClass>` block, `<term>` is
   retained, because that block enumerates the *index entries* of a
   subject register rather than the *occurrences* of references in
   running text.

### E2. `<persName>` and `<title>` in the running text retain TEI sub-specification rather than reducing to `<rs>`

heiEDITIONS prescribes that `PersonReference` and `WorkReference` are
carried on `<rs>`. Applied strictly, this would require rewriting
every `<persName ref="persons-heiEDITIONS.xml#anselm">` and every
`<title ref="works-heiEDITIONS.xml#proslogion">` in the running text
into `<rs>`, losing the TEI element-level distinction between persons,
works, and other entities.

**This edition keeps `<persName>` and `<title>` in the running text,
and adds `ana="hc:PersonReference"` / `ana="hc:WorkReference"`
respectively.** This is a deliberate, documented deviation from
heiEDITIONS' default usage.

**Reasons:**

1. `<persName>` and `<title>` are *semantically more specific* than
   `<rs>`. A consumer of the TEI data who does not know about
   heiEDITIONS still gets the right element-level distinction. A
   consumer who does know about heiEDITIONS gets the additional class
   membership via `@ana`. The opposite direction — start with `<rs>`
   and add a `@type` to recover the element-level distinction — would
   collapse two independent levels into one.
2. The DH market the project targets (German-language TEI-aware
   editorial infrastructures, theological publishing houses) expects
   `<persName>` / `<title>` in running text. Replacing them with `<rs>`
   would signal slower-than-community uptake, not more advanced uptake.
3. In the *register* files (`persons-heiEDITIONS.xml`,
   `works-heiEDITIONS.xml`), `<persName>` and `<title>` carry
   `ana="hc:PreferredAppellation"` / `ana="hc:AlternativeAppellation"`
   or `ana="hc:AlternativeTitle"`. heiEDITIONS explicitly permits these
   bindings on `<persName>` / `<orgName>` / `<title>` *in editorial
   register files*, so the deviation in question is restricted to the
   running text in `section1-heiEDITIONS.xml`.

This is the canonical example of what Holmes 2017 calls
**interoperability over interchange**: the data is machine-readable
by both communities, but is not byte-identical with the heiEDITIONS
default shape. See § "FAIR data" below.

### E3. `<entry>` elements inside `terms-heiEDITIONS.xml` are *not* bound to `SubjectReference` or any other heiEDITIONS class

The strict reading of heiEDITIONS Concepts: `SubjectReference` is a
**reference to** a concept, not the **definition of** a concept. The
TEI dictionary element `<entry>` is the latter. heiEDITIONS does not
currently publish a class for "concept entry in a subject register",
so `<entry>` elements stay unbound at the entry level. The enclosing
`<text>` carries `ana="hc:IndexOfSubjects"`, which captures the
register's *role* without overreaching into the entries themselves.

This restraint is deliberate. Adding `ana` everywhere a TEI element
might roughly fit some heiEDITIONS class would dilute the binding's
informational value. The principle is: **`@ana` is added only where
heiEDITIONS Concepts publishes a class whose `Verwendung`-clause
explicitly authorizes the host element**, and where the class's
definition actually fits.

### E4. The apparatus uses `VariantNote`, not generic `EditorialNote`

heiEDITIONS' note hierarchy is `EditorialNote → TextCriticalNote →
VariantNote`. Earlier drafts used the generic `EditorialNote`. The
heiEDITIONS variant uses the most specific applicable class,
`VariantNote`, because:

- The notes (`ed-a` to `ed-l`) record A/B/A2/GA witness divergences,
  not text-constitution decisions in general.
- The class definition of `VariantNote` — "Anmerkung, in der Lesarten
  (d. h. Varianten) zu einer Textstelle angegeben und ggf. kommentiert
  werden" — matches the content exactly.

Using the most specific class is the LOD analog of citing the most
specific source: it preserves information that a broader class would
discard.

### E5. Pre-heiEDITIONS variants are retained, not deleted

The repository keeps both the pre-heiEDITIONS files
(`fqi-barth-1931-section1.xml`, `terms.xml`, `persons.xml`,
`works.xml`) and the heiEDITIONS-bound variants. The reason is
methodological transparency: a reader can see what is added by the
heiEDITIONS binding by diffing the two versions, and the migration
path is documented by example. A future single-source workflow (one
canonical file, with the non-heiEDITIONS view generated by XSLT) is on
the roadmap (§ 5 below).

---

## FAIR data and the interchange / interoperability distinction

Following Martin Holmes, *Whatever happened to interchange?* (Digital
Scholarship in the Humanities 32, suppl. 1, 2017, pp. i63–i68), and
Šimek 2022 n. 14, this edition distinguishes two ambitions often
conflated under "FAIR":

**Interoperability** — the property that *external* systems (a GND
viewer, a TEI-aware search index, a digital Anselm edition, a
heiEDITIONS-aware visualizer) can do something useful with this
edition's data *as it stands*. The TEI markup, the
GND/VIAF/Wikidata identifiers in `persons-heiEDITIONS.xml` and
`works-heiEDITIONS.xml`, the standard TEI element names, the
**published LOD URIs from heiEDITIONS Concepts**, and the public XML
namespaces all support interoperability.

**Interchange** — the much harder property that a *different* TEI
project could absorb this edition's encoding decisions wholesale into
its own data model without information loss or model breakage. Holmes
argues, persuasively, that interchange in this strong sense is mostly
not achieved by TEI projects, and that the rhetoric of "TEI as a
shared standard" often overstates how much of the encoding is in
practice transferable.

The heiEDITIONS binding **shifts the prototype toward interoperability
**without** falsely promising interchange.** This claim has three
specific upshots:

- The `@ana` URIs point to a *published, dereferenceable* class
  ontology. A heiEDITIONS-aware tool can resolve them, retrieve the
  RDF, and discover what each class subclasses. This is more than
  TEI-element-name interoperability; it is concept-level
  interoperability.
- The deliberate deviations documented in Encoding Decisions E2 and
  E3 demonstrate, by example, that interchange in the strong sense
  is not claimed. A different theological edition project might
  legitimately reduce `<persName>` to `<rs>` in its running text, or
  might bind `<entry>` to a custom class. Both choices would be valid
  TEI, both would be valid heiEDITIONS bindings, neither would
  byte-interchange with this prototype.
- The pre-heiEDITIONS variants (Encoding Decision E5) preserve a
  fallback for consumers who cannot or do not want to resolve the
  heiEDITIONS URIs. This is a practical concession to the asymmetry
  between TEI-aware tooling (broad) and heiEDITIONS-aware tooling
  (narrow but growing).

**FAIR breakdown:**

- **Findability (F)** — GND / VIAF / Wikidata identifiers; unique
  `xml:id`s; `ana` attributes pointing to dereferenceable URIs.
- **Accessibility (A)** — plain TEI XML in a GitHub repository, no
  proprietary tooling required.
- **Interoperability (I)** — TEI namespace, heiEDITIONS Concepts
  namespace, GND authority files; documented `@ana` bindings.
- **Reusability (R)** — CC BY-NC-SA 4.0 on the encoding (the edited
  text remains under TVZ copyright; this is a portfolio prototype, not
  a publication).

In short: **this prototype is interoperable, in the heiEDITIONS sense,
but not "interchangeable" in the strong Holmes sense.** Acknowledging
this distinction matters more than papering it over.

---

## Technical conventions

### Encoding

- **TEI P5**, RELAX NG schema reference via `xml-model` processing
  instruction at the top of each XML file.
- **heiEDITIONS Concepts** namespace
  `xmlns:hc="https://lod.ub.uni-heidelberg.de/ontologies/heieditions/hc/current/"`
  declared on the root `<TEI>` element of every heiEDITIONS-variant
  file.
- All Latin material carries explicit `@xml:lang="la"`.
- Rendition classes (`#letter-spaced`, `#italic`) declared in
  `<tagsDecl>` and target CSS in the planned viewer.
- IDs use lower-case ASCII with hyphens (`cur-deus-homo` not
  `CurDeusHomo`); IDs that include German names lose umlauts
  (`juengel`, `stoevesandt`).

### File interlinking

Cross-file references use the form `<targetfile>#<id>`, e.g.
`<persName ref="persons-heiEDITIONS.xml#anselm">` or
`<rs ref="terms-heiEDITIONS.xml#intellectus-fidei">`. This is the
syntax expected by CETEIcean and by most TEI processors.

### `@ana` value notation

`@ana` values use the QName form `hc:SubjectReference`, etc., relying
on the `xmlns:hc` declaration on the root `<TEI>` element. The
expanded URI is
`https://lod.ub.uni-heidelberg.de/ontologies/heieditions/hc/current/SubjectReference`
in this case. heiEDITIONS itself uses the QName form throughout its
documentation; this project follows that convention.

### Apparatus

Parallel-segmentation `<app>` with `<lem>` (text of the base witness,
here `#GA` ≡ `#A2`) and one or more `<rdg>` with `@wit` listing
divergent witnesses. The base witness is never explicitly cited; only
divergences from it. This follows the editorial declaration of
GA II/13 itself (see `<editorialDecl>` items 1–8 in
`section1-heiEDITIONS.xml`).

---

## heiEDITIONS Concepts at a glance

The classes actually used in this prototype, grouped by file:

| File                                | Class                                | Carried by                              |
| ----------------------------------- | ------------------------------------ | --------------------------------------- |
| `section1-heiEDITIONS.xml` (body)   | `SubjectReference`                   | `<rs ref="terms-heiEDITIONS.xml#…">`    |
|                                     | `PersonReference`                    | `<persName ref="persons-heiEDITIONS.xml#…">` |
|                                     | `WorkReference`                      | `<title ref="works-heiEDITIONS.xml#…">` |
|                                     | `PrimarySourceQuotation`             | `<quote xml:lang="la">`                 |
|                                     | `AuthorialNote`                      | `<note type="authorial">` (Barth's footnotes) |
|                                     | `VariantNote`                        | `<note type="editorial">` (apparatus a–l) |
| `persons-heiEDITIONS.xml`           | `IndexOfPersons`                     | outer `<text>`                          |
|                                     | `IndexOfOrganizations`               | inner `<div>` wrapping `<listOrg>`      |
|                                     | `PreferredAppellation`               | primary `<persName>` / `<orgName>`      |
|                                     | `AlternativeAppellation`             | secondary `<persName>` / `<orgName>`    |
|                                     | `GNDURI`                             | `<idno type="GND">`                     |
| `works-heiEDITIONS.xml`             | `IndexOfWorks`                       | outer `<text>`                          |
|                                     | `PreferredAppellation`               | `<title type="full">`                   |
|                                     | `AlternativeTitle`                   | `<title type="abbr" | "alternative" | …>` |
|                                     | `GNDURI`                             | `<idno type="GND">`                     |
| `terms-heiEDITIONS.xml`             | `IndexOfSubjects`                    | outer `<text>`                          |
|                                     | `GNDURI`                             | `<ref target="…d-nb.info/gnd/…">`       |

For the full ontology, see
<https://heieditions.github.io/concepts/concepts.html>.

---

## Validation

Three layers of validation:

1. **XML well-formedness** with `xmllint --noout` (or any standard XML
   parser). All four heiEDITIONS files currently parse cleanly.
2. **TEI schema validation** against `tei_all.rng` (referenced in each
   file's `xml-model` PI). A project-specific ODD restricting the
   element set is on the roadmap (§ 5 below).
3. **Project-internal reference consistency.** A Python script (to be
   added under `scripts/`) will check that:
   - every `<rs ref="terms-heiEDITIONS.xml#…">` resolves to an
     `<entry xml:id="…">` in `terms-heiEDITIONS.xml`;
   - every `<persName ref="persons-heiEDITIONS.xml#…">` resolves to a
     `<person xml:id="…">` or `<org xml:id="…">` in
     `persons-heiEDITIONS.xml`;
   - every `<title ref="works-heiEDITIONS.xml#…">` resolves to a
     `<bibl xml:id="…">` in `works-heiEDITIONS.xml`;
   - forward-reference entries in `terms-heiEDITIONS.xml` are not, in
     fact, anchored in `section1-heiEDITIONS.xml`, and conversely
     half-proleptic entries do have at least one anchor;
   - every `@ana` value uses a recognized heiEDITIONS class name (no
     typos in QNames).

A future ODD customization will add Schematron rules that fold these
checks into schema validation itself.

---

## Roadmap

Listed roughly in dependency order. ✦ marks the next concrete step.

1. ✓ Minimal TEI template: full `teiHeader` and § 1.1 transcription
   demonstrating all five Šimek dimensions.
2. ✓ External `persons.xml` and `works.xml` with GND links.
3. ✓ External `terms.xml` with bilingual glosses and
   forward-reference entries.
4. ✓ README documenting the five-dimension structure and the
   FAIR / interchange distinction.
5. ✓ Migration of all four files to **heiEDITIONS Concepts** bindings
   via `@ana`; pre-heiEDITIONS variants retained for comparison.
6. ✦ ODD customization, generation of project-specific RELAX NG and
   Schematron schemas; Schematron rules that validate the encoding
   decisions E1–E4 mechanically.
7. Extension to three to five additional pages of transcription.
8. CETEIcean front-end with reading-view / source-view toggle
   (modeled on heiEDITIONS).
9. Single-source workflow: one canonical TEI file with the non-
   heiEDITIONS view generated by XSLT, replacing the dual-file
   maintenance pattern of Encoding Decision E5.
10. GitHub Pages deployment.
11. Polishing, portfolio documentation for applications.

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
- **heiEDITIONS Concepts** ontology referenced via `xmlns:hc`: see the
  upstream license at
  <https://heieditions.github.io/concepts/concepts.html>.

---

## Contact and citation

Citation (provisional):

> [Bearbeiter:in der Promotion], *Fides quaerens intellectum: A
> Digital Edition Prototype (heiEDITIONS-bound variant)*. Heidelberg,
> 2026. Available at: [URL of GitHub repository].

For questions about encoding decisions, see this README's "Encoding
decisions" section above (E1–E5) and the `<editorialDecl>` of
`section1-heiEDITIONS.xml`, which is the canonical place where
editorial choices are explained and motivated. Discrepancies between
this README and `<editorialDecl>` should be resolved in favor of
`<editorialDecl>`.

---

## References

- Karl Barth, *Fides quaerens intellectum. Anselms Beweis der Existenz
  Gottes im Zusammenhang seines theologischen Programms.* Edited by
  Eberhard Jüngel and Ingolf U. Dalferth. Karl Barth Gesamtausgabe,
  II/13. 1st ed. 1981, 3rd ed. Zürich: TVZ, 2002.
- F. S. Schmitt (ed.), *S. Anselmi Cantuariensis archiepiscopi Opera
  Omnia.* 6 vols. Seckau / Rome / Edinburgh: Nelson, 1938–1961.
- heiEDITIONS Concepts. Heidelberg University Library. <https://heieditions.github.io/concepts/concepts.html>.
- Martin Holmes, "Whatever happened to interchange?" *Digital
  Scholarship in the Humanities* 32, suppl. 1 (2017): i63–i68. doi:
  10.1093/llc/fqx034.
- Jakub Šimek, "heiEDITIONS — eine Heidelberger Infrastruktur für
  Editionen (nicht nur) mittelalterlicher Texte." *Beiträge zur
  mediävistischen Erzählforschung. Themenheft* 12 (2022): 27–46.
- TEI Consortium, *TEI P5: Guidelines for Electronic Text Encoding and
  Interchange.* Version 4.x. <https://tei-c.org/release/doc/tei-p5-doc/en/html/>.
