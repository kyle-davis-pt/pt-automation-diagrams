# Fidelity note — `system-map.drawio`

**Last verified: 12 Oct 2026.** Converted from n8n workflow `kxlyu6VRQqtR9mSb` at `updatedAt`
2026-09-07T23:59:25.638Z, `versionId` 405f7e22-f437-4ec3-ae7b-e7d69bdeaf58.

## The sentence that matters

**Any edit you make in draw.io is destroyed the next time an agent regenerates this file, and it
never reaches the live page.** The flow is one-way: n8n Code node → `.drawio`. There is no importer
back into n8n, and `https://n8n.profitabletradie.tech/webhook/pt-map` does not read this file — it
builds its HTML from its own Code node. The sticky note on the workflow says so outright: *"The Code
node is the CANONICAL SOURCE of the Lead Magnet map — edit its NODES/EDGES arrays to update the live
page."*

So: **to change the map, change n8n.** Use draw.io to look, to rearrange for a conversation, to
export a picture. Treat anything you type into it as scratch until the storage/round-trip question
is decided.

## Source of record

Read-only `GET /api/v1/workflows/kxlyu6VRQqtR9mSb` — **System Map Server (HTML pages)**, active, 6
nodes: `Serve Map` (webhook, GET `pt-map`, responseMode `responseNode`) → `Page Part 1 (shell)` →
`Page Part 2 (data)` → `Page Part 3 (app)` → `Respond HTML`, plus one sticky note. The map data —
the `PAGES` object with `nodes[]` and `edges[]` — lives in the Code node **Page Part 3 (app)**.

I pinned both ends rather than trusting one. I also fetched the live page and compared the served
`PAGES` block against the block in the workflow definition: both 4,542 characters,
sha256 `f79c77e55092ec90…` on both sides, byte-identical. The definition I converted is the thing the
page is actually serving, as of this run.

Nothing was written to n8n. One `GET` on the API, one `GET` on the public webhook page.

## Files

- `system-map.drawio` — the deliverable. Attachment `af6a1604-8430-4386-88f9-dab71e4cb5da`,
  sha256 `c048a0972befa9187a5ac728867a9da8452758e4ff603fa92755b31319a7bdcc`, 18867 bytes. Also inline in the **system-map.drawio** document on this task.
- `convert-system-map-to-drawio.js` — the regenerator. One read-only `GET` on n8n; reproduces the
  delivered file byte-for-byte. Supporting, not a deliverable.
- `verify.py` — the round-trip check described below.

## Converted exactly

Diffed by a parser independent of the one that wrote the file (generated in Node, re-parsed and
compared in Python against the live source arrays). **Zero differences:**

| | source | in `.drawio` |
|---|---|---|
| nodes | 10 | 10 `<mxCell vertex="1">`, `x`/`y`/`width`/`height` identical |
| edges | 9 | 9 `<mxCell edge="1">`, `source`/`target`/label identical |
| label lines | 46 | 46, text byte-identical, each in its own `<font color>` |
| text colours | 7 | 7, preserved per line |
| fill colours | 4 | 4, preserved per box |

Cell ids are derived from the source ids — `node-form`, `node-dio`, `edge-form-del` — so a
regenerated file diffs cleanly against an edited one instead of churning random GUIDs.

Edge anchors are not guessed: I re-implemented the live page's `updateEdges()` side-selection rule
and baked the result into fixed `exitX/exitY/entryX/entryY`. At the source coordinates all 9 edges
resolve to right-side → left-side, which is what the page draws.

## Approximated — named, not dropped

1. **3-digit hex expanded to 6** (`#fff` → `#ffffff`, `#555` → `#555555`). Identical colour; draw.io's
   colour picker prefers the long form.
2. **Curve shape.** The page draws an explicit cubic bézier with 0.4 control offsets; draw.io uses
   `curved=1` and its own smoothing. Same endpoints, slightly different bow.
3. **Arrowhead colour.** On the live page the marker is filled `#555` on a `#999` line. mxGraph paints
   the marker with the stroke colour, so arrowheads are `#999` here.
4. **Edge label placement.** The page puts the label at the midpoint, 5px above the line. draw.io
   centres it on the line; I used `verticalAlign=bottom` with a white label background to approximate.
5. **Text wrapping — one box affected.** The live page is SVG text with no wrapping: long lines simply
   overflow the box edge. draw.io wraps. Measured every line against its box: the only one that
   overflows is the title of the `dio` node, *"[DIALER.IO] Opt In Leads 4806 (NA/CA) / 4807 (AU/NZ)"*,
   roughly 380px of usable 368px. In draw.io it wraps to a second line; the box has vertical headroom
   so nothing is hidden. Every other title and all 46 label lines fit.
6. **Line spacing.** The page steps baselines by a fixed 16px; draw.io uses HTML line-height. Lines
   inside a box sit a few pixels off from the live page. Box positions and sizes are exact.
7. **Rounded corners.** Page `rx=10`; here `absoluteArcSize=1;arcSize=20`, which is a 10px radius.

## On the live page, with no diagram equivalent — not converted

- **The left sidebar: 116 entry-point forms**, each with submission count, days since last
  submission, and direct-workflow count, encoded in the `SIDEBAR` string in Code node
  **Page Part 2 (data)**. Its own stats line: 116 forms, 33 active in the last 180 days, 34 with no
  directly-connected workflow. This is a dataset and an index, not a diagram, so it is not in the
  `.drawio`. If it is wanted as a diagram artifact, say so and it becomes a separate task — it should
  not be hand-maintained in two places.
- **Exactly one of those 116 has a map built.** `slugFor()` returns a slug only for *Lead Magnet
  Landing Page Download-v1*. Every other form shows the placeholder panel. So this `.drawio` is the
  whole of the drawn map — there is no second page I left behind.
- **Search box, badges** (`dormant` / `active` / `no direct wf` / `MAP BUILT`) **and the placeholder
  panel.** Page chrome.
- **Pan, zoom, drag, "Reset layout".** draw.io has all of these natively; nothing to convert.
- **Saved layouts are browser-local.** Dragging boxes on the live page writes to `localStorage` under
  `ptmap:lead-magnet-v1` — it never goes back to n8n. This `.drawio` carries the *source* coordinates.
  If you rearranged the live map on your own screen, this file will not match what you see.
- **Page header and legend** are converted, into two cells clearly marked as additions: `meta-header`
  and `meta-legend`. Both sit outside the source bounding box (`y=-130` and `y=1190`) so that not one
  converted node had to move off its original coordinates.

## The convention: how certainty is encoded

**Fill colour says what kind of thing the box is:**

| fill | meaning |
|---|---|
| `#e9e0f7` | form / list (a HubSpot object, not a workflow) |
| `#ffffff` | HubSpot workflow, ON |
| `#ececec` | HubSpot workflow, OFF / legacy |
| `#fff8e6` | open-questions panel |

**Font colour of each label line says how certain that line is.** One claim per line; to change a
line's status, change its font colour and nothing else.

| font colour | meaning | uses |
|---|---|---|
| `#1f6e43` green | VERIFIED — read from the live definition | 7 |
| `#2e5395` blue | trigger / enrolment condition | 7 |
| `#8a5300` amber | INFERRED, or an open question | 8 |
| `#555555` grey | plain statement, no verification claim | 20 |
| `#c0392b` red | known defect or risk | 1 |
| `#777777` grey | secondary / superseded detail | 2 |
| `#999999` grey | raw identifier | 1 |

This is written into the file itself as the `meta-legend` box, so it travels with the diagram.

**One flag on the convention.** The brief names four colours; the data uses seven. The three extra
ones are real and carry meaning — in particular `#c0392b` is used exactly once, on *"phone: plain
field, NO validation, NO country"*, which is the only line on the map that flags a defect rather than
describing behaviour. I preserved all seven and labelled the extra three in the legend as **observed
usage, not an upstream definition**, because nothing on the live page states what they mean. If the
convention is meant to be four colours, three lines need re-colouring **upstream in n8n**, not here.

## Round-trip test — what I verified and what I did not

**Verified:**

- Plain, uncompressed XML (not the deflate+base64 form draw.io also accepts), so both **File → Open**
  and **Extras → Edit Diagram** work on it, and it diffs in git.
- Parses strictly as XML. One `<diagram>`, one `<mxGraphModel>`, the mandatory `0`/`1` root cells, 23
  cells, no duplicate ids, every edge `source`/`target` resolving to a real cell, every cell parented
  to the default layer.
- The independent round-trip described above: 10/10 nodes, 9/9 edges, 46/46 label lines, every
  coordinate and every colour, zero differences against the live source.
- **Deterministic.** The mxfile `modified` stamp is the source workflow's `updatedAt`, not the clock.
  Two consecutive runs produce byte-identical files, and the standalone converter — which reads the
  n8n *definition* and never touches the live page — reproduces the delivered file byte-for-byte.
  That matters for the regeneration story below: a regeneration that changes nothing produces a
  zero-byte diff, so any diff you see is a real upstream change. It also re-confirms the
  definition ↔ served-page equivalence by a second, independent route.

**Not verified — I have no browser, so I did not see it rendered.** "It renders" is not a claim I can
make. One **File → Open** at app.diagrams.net is the missing ten seconds of the test.

I tried to close that gap headlessly with the real mxGraph codec (`mxgraph` + `jsdom` from npm) and it
is **not a usable check**: it drops every child cell. Before concluding anything from that, I ran a
control — a 5-cell file written in draw.io's own canonical shape — through the same harness. It fails
identically: both the control and my file report `model.cells: 2` and `layer children: 0`. The harness
is broken for *every* draw.io file, so it says nothing about mine in either direction. I am recording
it because the failure looks exactly like a defect in the output and is not one.

**What a human edit does to a regenerated file.** Three distinct losses, worth separating:

1. **Regeneration overwrites.** The generator reads n8n and writes the whole file. An operator edit —
   a moved box, a reworded line, a new note — is gone. There is no merge step and nothing warns you.
2. **Your edit never reaches the live page.** Even before any regeneration, the webhook page is built
   from the n8n Code node. The two diverge from the moment you save in draw.io.
3. **A no-op open-and-save still rewrites the file.** draw.io re-serializes on save: it may compress
   the payload depending on your settings, reorders style keys, and normalizes the HTML in labels.
   Expect a large diff from opening and saving without changing anything. Stable cell ids are what
   keep a later comparison readable — the box that moved is still `node-dio`.

## If this should stop being one-way

Not my call — operator decides. The three options, with the failure mode of each:

- **(a) n8n stays canonical, the `.drawio` is regenerated.** Operator edits in draw.io are scratch.
  Costs nothing, and the map stays true. The live page keeps working.
- **(b) The `.drawio` becomes canonical and the n8n Code node is generated from it.** A real
  round-trip, but it needs a writer back into n8n — AutomationEngineer's work, and the n8n write gate
  applies.
- **(c) Both are edited by hand and kept in sync.** The failure is silent drift: nothing errors, the
  two maps just stop agreeing, and the one you happen to open looks authoritative. A map people trust
  and that is wrong is worse than no map. I'd flag this as the one that quietly breaks.

## Out of scope on this task

No new findings were added to the map. Every claim inside the `.drawio` is the live page's claim as of
7 Sep 2026, including its own open questions — not mine. New findings belong to PRO-4 and PRO-19, and
keeping them separate is what makes a conversion error distinguishable from a new claim.