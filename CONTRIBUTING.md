# Contribute to the developer guide

This repository explains EmberBSD across implementation owners.
Write for a developer who needs the concept before reading source code.

## Choose the document

Use `concepts/` for architecture and modularity, `modules/` for component explanations,
`configuration/` for selection boundaries, and `development/` for navigation and terminology.
Keep detailed software contracts, command references, and tests beside their implementation.
Keep hardware support and wiring records in DevBoards.

## Separate concepts from implementation

Source code is also documentation.
Code, comments, headers, schemas, tests, help, and owner references explain exact implementation behavior.
Use this guide for module purpose, responsibilities, interactions, design reasons, and configuration choices.
Explain why a boundary exists and what a choice changes for its consumers.

Link function signatures, field/default lists, configuration files, command recipes, and algorithm details to their implementation owner.
Keep those exact references there. Avoid reproducing them or narrating code line by line here.
Interaction diagrams and composition examples belong here when they explain a concept.
They must not become another API, configuration, or command reference.
Keep non-obvious invariants, resource ownership, and error semantics documented beside the implementation.

## Prepare a change

Check the current published owner sources before changing a technical claim.
Use short English sentences and explicit component names.
Label proposed interfaces and meaningful implementation limits.
Link to the canonical contract instead of copying its reference material or support matrix.
Identify the source revision in [sources.md](sources.md) when updating its review baseline.
Preserve attribution and licenses for adapted material.

When behavior changes, update the implementation owner's contracts, checks, and help.
Update this guide when purpose, boundaries, dependencies, interactions, or configuration choices change.
For an exact parameter or internal algorithm change, update owner references and any affected links here.
Update skill routing when the owner or entry point changes.
Keep personal-patch explanations in the selected personal repository unless a shared contribution is requested.

## Check and submit

Check relative links, heading anchors, external source paths, tables, and code fences.
Check that conceptual claims match the current implementation and its documented limits.
Follow links to exact interfaces and confirm that their owner references exist.
Keep credentials and private installation details outside the guide.
Review Mermaid diagrams in a renderer that supports them.
Run `git diff --check` and inspect the complete proposed diff.

Submit a pull request with the problem, changed pages, reviewed sources, and checks performed.
Disclose AI assistance when used.
An independent reviewer checks the explanation, ownership, sources, and scope of claims.
Documentation-only changes require no OS build unless they also change build inputs or behavior.

New guide material uses [BSD-2-Clause](LICENSE).
Existing owner documents and imported excerpts keep their original licenses and attribution.
