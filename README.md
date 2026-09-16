# OSPAR Guidelines v2.0 — OSCAL Catalog (unofficial)

A machine-readable version of the Association of Banks in Singapore (ABS) *Guidelines on Control Objectives and Procedures for Outsourced Service Providers*, Version 2.0 (25 March 2024), in [OSCAL](https://pages.nist.gov/OSCAL/) catalog format.

> **Unofficial.** ABS did not produce or endorse this catalog. It was generated automatically from the published PDF. **The PDF is the authoritative text.** For audit, contract or regulatory purposes, check any requirement against the source.
>
> Source: <https://abs.org.sg/docs/library/abs-ospar-guidelines-v2-0.pdf>
> SHA-256 of the source PDF used: `635b02875de69e3cc1de1579b70facd5f4c19edf127bd2e3769e8a92f70b360b`

---

## Files

| File | Use |
|---|---|
| `ospar-v2.0-catalog.json` | OSCAL catalog, JSON (primary) |
| `ospar-v2.0-catalog.xml` | Same catalog, OSCAL XML |
| `ospar-v2.0-catalog.yaml` | Same catalog, OSCAL YAML |
| `ospar-v2.0-catalog-activities.csv` | Flat view, one row per statement/item/note. For review, spreadsheets and crosswalks. **Not an OSCAL file.** |

All three OSCAL files carry the same content. Pick whichever format your tooling expects.

**OSCAL version:** the files declare `1.1.3`. They also validate against the NIST 1.2.1 JSON and XML schemas.

---

## What's in the catalog

| Group | Source section | Controls |
|---|---|---:|
| `ospar-ai` | Audits and Inspections (I–VI) | 6 |
| `ospar-elc` | I. Entity Level Controls ((a)–(g)) | 7 |
| `ospar-gitc` | II. General IT Controls, 12 section sub-groups ((a) Logical Security … (l) Software Application Development and Management) | 16 |
| `ospar-slc` | III. Service Level Controls, 6 section sub-groups ((a) Setting up of New Clients/Processes … (f) Business Continuity Management) | 12 |
| `ospar-defs` | Definitions (12 terms, stored as parts, not controls) | – |

Totals: 41 controls and 310 enumerated items (i., a., bullets, numbered). 17 items are flagged cloud-specific.

Front matter from the PDF is kept in the metadata. The Introduction and Scope are in `metadata.remarks`. The Version History (1.0, 1.1, 2.0) is in `metadata.revisions`.

### What a "control" means here

The source has three different structures, and the catalog maps each one differently:

- **General IT Controls (II) and Service Level Controls (III).** Each section (e.g. II(a) Logical Security) is a **group**. The section's "These controls provide reasonable assurance…" statement is a part named `objective` on that group. Each **numbered control objective** (1., 2., …) is a **control**. Its control activities (i., ii., …) and sub-items (a., b., bullets) sit inside the control's `statement` part. This matches how OSPAR audit reports qualify results against control objectives.
- **Entity Level Controls (I).** Each component (a)–(g) is a control. The source here is mostly prose, so the statement holds paragraphs and roman-numbered lists in source order.
- **Audits and Inspections.** Each heading I–VI is a control (class `audit-requirement`). These are requirements on the audit regime (auditor criteria, audit frequency, reporting), not controls the OSP operates. Filter on `class` if you only want operational controls.

### Control anatomy (II and III)

```
control  ospar-gitc-a-1                     "Logical access to programmes, data, … need-to-have basis"
├─ part objective   ospar-gitc-a-1_obj      control objective text (left column of the source table)
├─ part statement   ospar-gitc-a-1_smt
│   ├─ item  _smt.i         i.   control activity
│   │   ├─ item  _smt.i.a     a.   sub-item
│   │   │   └─ item  _smt.i.b.b1   bullet (no label)
│   │   └─ note  _smt.vi.note1     "Note: …" inside an activity
│   └─ item  _smt.x         x.   (Cloud specific control …)  ← flagged
└─ part note        ospar-gitc-b-1_note1    footnote from the objective column (e.g. "*Controlled area refers to…")
```

**Control titles.** For numbered objectives, the title is the objective sentence, repeated in the `objective` part. The Sample Controls III(b)4–6 are the exception: their title is the short heading (e.g. "Sample Controls for Data Entry Services") and the objective is separate.

---

## Identifiers

IDs are stable and derived from the source numbering, so you can build them without looking them up.

| Pattern | Example | Source reference |
|---|---|---|
| `ospar-ai-{n}` | `ospar-ai-3` | Audits and Inspections III |
| `ospar-elc-{letter}` | `ospar-elc-g` | I(g) |
| `ospar-gitc-{section}-{objective}` | `ospar-gitc-h-2` | II(h) objective 2 |
| `ospar-slc-{section}-{objective}` | `ospar-slc-b-4` | III(b) objective 4 |
| `{control}_obj` / `_smt` | `ospar-gitc-k-1_smt` | objective / statement |
| `{control}_smt.{roman}.{alpha}` | `ospar-gitc-a-1_smt.i.e` | II(a)1 activity i, sub-item e |
| `…​.b{n}` | `ospar-gitc-a-1_smt.i.e.b3` | third bullet under that sub-item |
| `…​.p{n}` / `…​.note{n}` | `ospar-gitc-k-1_smt.iv.p1` | unnumbered paragraph / note, in order |
| `{control}_smt.l{run}-{label}` | `ospar-elc-g_smt.l2-iv` | only where a list restarts within one control (I(g) has two i.–vi. lists) |
| `ospar-def-{term-slug}` | `ospar-def-service-provider` | Definitions |

Every group and control also has:

- a `label` prop in source notation: `II(a)1`, `I(g)`, `AI.III`
- a `sort-id` prop for ordering

Items carry their source marker as `label` (`i.`, `a.`).

UUIDs are deterministic. Regenerating from the same PDF gives the same UUIDs.

---

## Properties you can filter on

Standard OSCAL props (no namespace): `label`, `sort-id`.

OSPAR-specific props use namespace **`urn:x-ospar-oscal:ns`**:

| Prop | On | Values | Meaning |
|---|---|---|---|
| `cloud-specific` | item | `true` | Activity is marked "(Cloud specific control – …)" in the source |
| `cloud-scope` | item | verbatim text | The scope as worded in the source. ABS words it five different ways, e.g. "CSPs and OSPs offering PaaS/IaaS/SaaS solutions". |
| `cloud-service-models` | item | space-separated subset of `IaaS PaaS SaaS` | Normalised version of `cloud-scope`, for filtering |
| `applies-to-csp` | item | `true` | Scope names CSPs |
| `contains-cloud-specific` | control | `true` | Control has at least one cloud-specific item. Currently II(a)1, II(c)1, II(e)1, II(f)1, II(g)1 and II(l)1. |
| `applicability` | control | `conditional` | Sample Controls III(b)4–6. They apply only to data entry, debt collection or statement printing providers. The matching "Note: …" text is in an `applicability` part. |
| `service-type` | control | text | The service a conditional control applies to |
| `alt-term` | definition part | text | Alternative term (e.g. "outsourced service provider") |
| `transformation` | metadata | `unofficial` | Marks this catalog as a non-ABS artefact |

Non-standard part names (`note`, `paragraph`, `applicability`, `definition`) use the same namespace.

---

## Using it

### Examples with jq

```bash
F=ospar-v2.0-catalog.json

# All controls: id and title
jq -r '.. | objects | select(has("controls")) | .controls[] | "\(.id)\t\(.title)"' $F

# One control in full
jq '.. | objects | select(.id=="ospar-gitc-k-1")' $F

# Cloud-specific activities that include SaaS
jq -r '.. | objects | select(.name=="item" and any(.props[]?; .name=="cloud-service-models" and (.value|contains("SaaS")))) | .id' $F

# Conditional (sample) controls
jq -r '.. | objects | select(has("controls")) | .controls[] | select(any(.props[]; .name=="applicability" and .value=="conditional")) | .id' $F

# A definition
jq -r '.catalog.groups[] | select(.id=="ospar-defs") | .parts[] | select(.title=="service provider") | .prose' $F
```

### Tailoring with an OSCAL profile

To define your own baseline (e.g. only the controls relevant to a cloud SaaS service), write a profile that imports this catalog and selects controls by ID:

```json
{
  "profile": {
    "uuid": "00000000-0000-4000-8000-000000000001",
    "metadata": {
      "title": "OSPAR v2.0 - cloud-relevant General IT Controls",
      "last-modified": "2026-09-16T00:00:00+10:00",
      "version": "0.1",
      "oscal-version": "1.1.3"
    },
    "imports": [{
      "href": "ospar-v2.0-catalog.json",
      "include-controls": [{
        "with-ids": ["ospar-gitc-a-1", "ospar-gitc-c-1", "ospar-gitc-e-1",
                     "ospar-gitc-f-1", "ospar-gitc-g-1", "ospar-gitc-l-1"]
      }]
    }],
    "merge": { "as-is": true }
  }
}
```

This example validates against the OSCAL 1.1.3 profile schema. It has not been resolved through a profile resolver. Replace the placeholder UUID.

To leave out the conditional Sample Controls, exclude `ospar-slc-b-4`, `ospar-slc-b-5` and `ospar-slc-b-6` unless your service is one of those types.

### Granularity caveat for SSPs and assessments

Controls are at the **control objective** level, and some are large: II(a)1 has 19 activities and II(l)1 has 8, each with sub-items. If you record implementation or test results per activity, target the item part IDs (e.g. `ospar-gitc-a-1_smt.xiii`) with `by-component` / `statement-id`, rather than the control as a whole.

---

## Fidelity and known limitations

- **Text is as published.** Wording, spelling and grammar are copied from the PDF, including the source's own errors: "Fl’s" (lowercase L) for "FI’s", "the following controls exists", "Criteria for Qualification External Auditor". The I(f) heading says "Human Resource Policies and **Procedures**", while the component list on the preceding page says "…and **Practices**". The catalog uses the heading.
- **Heading case.** Audits and Inspections headings are all capitals in the source and have been converted to title case.
- **Paragraph breaks** inside an item are inferred from the page layout. A misplaced break changes where a paragraph splits. It does not change the wording or the item hierarchy.
- **Definitions are flat prose.** Lettered and roman lists inside a definition are separate paragraphs, not nested parts. A closing clause that covers the whole list (e.g. "that provides a relevant service to the bank" in *service provider*) is its own paragraph after the list. Read it as applying to every item above it.
- **Duplicate definition removed.** The PDF defines "outsourced relevant service" twice with identical text. The catalog keeps it once.
- **Out of scope.** The separate OSPAR audit report *template* referred to in Audits and Inspections IV is not part of this document or this catalog.
- **Prose markup.** Prose fields are OSCAL markup. Literal asterisks from the source (footnote markers such as "Areas\*") are escaped as `\*` in JSON and YAML.

The quality report for this release (`qa-report.md`) records schema validation results, per-section counts and a word-level reconciliation against the source. Its key check: the number of enumerated items matches an independent count of list markers in the PDF, section by section.

## Versioning

| Catalog `metadata.version` | Source document |
|---|---|
| `2.0` | ABS OSPAR Guidelines v2.0, 25 March 2024 |

`metadata.version` follows the **ABS document version**. `metadata.last-modified` is when the catalog was generated. A regeneration from the same source that corrects extraction issues keeps version `2.0` and gets a new `last-modified`. See this repository's release notes for what changed.

## Reporting issues

If the catalog differs from the PDF (missing or misplaced text, wrong nesting, wrong flag), open an issue with the control or part ID and the PDF page number.

## Copyright and use

The requirement text belongs to its publisher, The Association of Banks in Singapore. This repository adds structure and metadata only.

## Attribution

The OSCAL derivative artifacts in this repository are created by independent security and compliance engineer Baden Hughes. They are a mechanical transformation of published material and add no framework content.

This is an unofficial conversion. It is not endorsed by, affiliated with, or supported by The Association of Banks in Singapore.
