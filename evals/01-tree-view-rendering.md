---
name: tree-view-rendering
domain: web
difficulty: medium
---

## Prompt

Our file explorer tree view starts lagging noticeably once a project has more
than 8,000 nodes. We already virtualize the visible rows with react-window, but
scroll jank appears when expanding large subtrees because we recompute the flat
row list on every toggle. Standard suggestions — memoization, smaller
components, incremental rendering — have not moved the needle meaningfully.
What would a fundamentally different rendering approach look like?

## Context

- React 18, TypeScript, Electron desktop app (so we have full Node access in
  the main process).
- The tree data is an immutable snapshot pushed from the main process on each
  file-system change; the renderer owns display state (expanded, selected).
- Subtree expansion can reveal 2,000+ nodes in a single toggle event.
- We are willing to rewrite the renderer layer; the data model is locked.

## Expected shape

- Must produce 3 approaches labeled conservative / medium / wild
- Must list at least 3 load-bearing assumptions with inversions
- Wild approach must invert at least one of those assumptions (e.g., "the DOM
  is the right substrate for this list" or "React must own the render loop")
- Must include a recommendation tied to leverage x blast radius
- Must flag uncertainties or what would need measurement (e.g., actual jank
  source, whether it's layout, paint, or JS)

## Red flags (signs the prompt has regressed)

- Only proposes one approach
- Recommends a different virtualization library without questioning whether
  virtualization is the right frame at all
- Claims specific FPS numbers without noting they are estimates
- Does not surface the assumption that DOM/React must own the list
- Wild approach is just "use a canvas" with no sketch of how it would work

## Notes

This fixture deliberately mirrors the tree-view example in the agent's own
frontmatter, making it a known-good sanity check for the most basic case.
