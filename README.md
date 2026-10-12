# pt-automation-diagrams

draw.io diagrams of Profitable Tradie's business logic — HubSpot workflows and assets, n8n
workflows, and the systems around them. Owned by Automation & Systems.

Private repo. Created 12 Oct 2026.

## How to open a diagram

A `.drawio` file is mxGraph XML. Two ways in:

- **app.diagrams.net → File → Open from → GitHub**, pick this repo. Edits save back as a commit.
- Or download the file and open it locally.

## The rule that governs everything here

**A diagram generated from a live system is a rendering, not a source.** If you edit it in
draw.io and an agent later regenerates it, your edits are replaced.

Git is the defence. A regeneration arrives as a **pull request**, not a push to `main`. You can
read the diff and reject it. That is the whole reason these files live here and not in Drive.

- `system-map.drawio` is generated from n8n workflow `kxlyu6VRQqtR9mSb`. **To change the map,
  change n8n.** The `.drawio` never flows back, and it is not what
  `n8n.profitabletradie.tech/webhook/pt-map` serves — that page builds its HTML from its own
  Code node.

## Layout

```
diagrams/          the .drawio files
  <name>.drawio
  <name>.md        fidelity note: source, what was verified, what was dropped
```

Every generated diagram ships with a fidelity note beside it. The note states the source object,
its `updatedAt` and `versionId` at the moment of conversion, and anything the conversion could not
carry. A diagram without a note is not finished.

## Rules for agents

1. **Never push a regenerated diagram to `main`.** Open a pull request.
2. **Re-read the source before regenerating** and pin its `updatedAt` and `versionId` in the
   fidelity note. A diagram that spans an edit of its source is void.
3. **Provenance by border and bracket tag, never by colour.** Colour already carries meaning
   inside these maps. Use a dashed border plus a `[INFERRED]` / `[VERIFIED]` / `[STALE dd mmm]`
   tag in the label.
4. **Carry the operator's own marks forward unchanged.** Do not promote an INFERRED claim to
   VERIFIED without naming the config read that did it.
5. **Map what exists, not what should exist.** Anything that looks wrong is recorded as a named
   gap. Nobody draws the fixed version.
6. **Never commit a credential, token, API key or contact record.** Diagrams name systems, flows,
   lists and properties — not people.
