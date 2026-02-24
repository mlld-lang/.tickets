---
id: m-c3b6
status: closed
deps: []
links: []
created: 2026-02-23T23:37:01Z
type: epic
priority: 2
assignee: Adam
tags: [docs, website, atoms]
updated: 2026-02-24T11:37:34Z
---
# Rebuild website from atoms + explainers

Replace docs/user/*.md as the website source with docs/src/atoms/ and docs/src/explainers/.

## Current pipeline

convertDocs.js copies docs/user/*.md to website/src/docs/*.md, adding 11ty frontmatter. Eleventy builds with hardcoded slug-to-order nav. markdown-it + prism for rendering. 21 docs, 21 flat pages.

## New pipeline

Rewrite convertDocs.js to read from two sources:
- docs/src/explainers/*.md -> top-level standalone pages (introduction, security)
- docs/src/atoms/**/*.md -> assembled into ~10 category pages (one per category)

## Assembly strategy

Each category page is built by:
1. Category _index.md content as the page intro
2. Atoms grouped by `parent` field, with parent-level _index.md content as section intros
3. Child atoms as subsections within each group
4. Standalone atoms (no parent) as their own sections
5. Strip `## tldr` sections from atoms (those are for howto, not website flow)

Uses mlld section selector syntax for stripping: `<file.md # section; !# tldr>`

## Section intro atoms

Many parent groups need brief introductory text that provides connective tissue between assembled atoms. Some _index.md files already exist at category level. Parent-level _index.md files (e.g. for when/, for/) may need creation so the assembled output reads like documentation, not a concatenated reference card.

## Nav changes

Replace hardcoded slug-to-order map in .eleventy.js with category-based ordering from atom frontmatter. Sidebar gets section headers for categories. Explainers sort first.

## Approach

Pilot with one category (control-flow has clear parent groups), verify assembly quality, then migrate remaining categories.

## Not blocking on

Filesystem restructure (flat to nested dirs). Assembly uses `parent` frontmatter field to group atoms regardless of filesystem layout. Nested dir restructure is a nice-to-have that makes the filesystem self-documenting.
