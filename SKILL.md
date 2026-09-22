---
name: obs-read-paper
description: Read a user-provided research paper PDF inside an Obsidian vault and create a deep, source-grounded, figure-supported Markdown report with clickable PDF++ links for every page, line, figure, table, and equation reference. Use when the user asks to obs阅读论文, obs总结论文 or obs + any summary of a paper. Requires a paper source and an output Markdown path; if either is missing, ask the user before proceeding.
---

# Obsidian Paper Reading Report

Create a deep Markdown reading report from a provided paper PDF and save it into the user's Obsidian vault.

## Required inputs

Before doing extraction or writing, identify:

1. **Paper source**: a PDF path from `@file`, `<current_note>`, explicit path, or user-provided attachment.
2. **Output path**: the target `.md` path in the vault.

If the paper source or output path is not clearly specified, ask a concise clarification question. Do not invent a storage path. If the source is inferable from `@...pdf` or the current note and the output path is explicitly given, proceed.

Use relative vault paths for vault files. Use absolute paths only for external context explicitly provided by the user.

## Related skills to use

Use these skills as needed, in this order:

- **`pdf`**: extract text, page numbers, tables, captions, and render pages/figures when layout or visuals matter.
- **`deep-research`**: structure the analysis as a rigorous, source-grounded research report, but restrict evidence to the provided paper unless the user explicitly authorizes external research.
- **`nature-reader`**: use only when the user asks for full-paper reading, bilingual/原文对照 translation, paragraph-level source maps, or figure/table-aware reader artifacts. Do not create a full bilingual reader for a summary-only request.
- **`obsidian-markdown`**: create valid Obsidian Markdown with frontmatter, wikilinks, callouts, embeds, equations, Mermaid diagrams, and clickable vault references.
- **`obsidian-cli`**: inspect PDF++ state, derive PDF text-selection indices, resolve links, and test navigation inside the running Obsidian app.
- **`picgo-image-upload`**: crop required paper figures/tables, upload them through PicGo, and insert only the returned Markdown image syntax. If upload fails, do not insert temporary paths or placeholders.

## Source discipline

- Read only the provided PDF when the user says “仅阅读我提供的论文/PDF” or similar.
- Do not browse, search, or verify external citations unless the user explicitly requests external checking.
- Attribute substantive claims to the paper with clickable PDF++ evidence links. Never leave a page, line, figure, table, or equation locator as plain text.
- Separate the paper's claims from your own review comments. Label critique as “审读提示”, “局限”, or “审稿关注点”.

## PDF++ evidence links

Use vault-relative PDF paths and preserve the concise evidence pointer as display text:

```markdown
[[folder/paper.pdf#page=3|p.3]]
[[folder/paper.pdf#page=4&selection=12,0,14,38|p.4 L227]]
[[folder/paper.pdf#page=4&selection=20,0,24,9|p.4 Figure 2]]
[[folder/paper.pdf#page=7&selection=33,0,37,12|p.7 Table 3]]
[[folder/paper.pdf#page=6&selection=45,0,47,4|p.6 Eq. (7)]]
```

Apply these rules:

1. Link every evidence pointer, including pointers in prose, tables, callouts, visual captions, and source notes.
2. Use `#page=<PDF viewer page>` for page-only evidence. Use the physical page index recognized by Obsidian, even when the printed page label differs.
3. Add `&selection=<beginIndex>,<beginOffset>,<endIndex>,<endOffset>` when the cited text, caption, table label, figure label, or equation is selectable. Obtain indices from the PDF's actual PDF.js text layer through PDF++ or from a native copied selection; never estimate or fabricate them from `pdftotext` character counts.
4. For a line range, link to and select only the first cited line while keeping the full range in the display text, for example `p.4 L227-L260`. For a page range, link to only the first page while keeping the full range, for example `pp.15-17` with `#page=15`.
5. For a figure or table spanning multiple pages or panels, target its first cited page and select its first label or caption text. For an equation spanning multiple lines, select the equation number when available; otherwise select its first selectable token.
6. For non-contiguous evidence such as `p.5, pp.15-16`, create separate links for each component so each first location is clickable.
7. If exact selection is impossible because the PDF lacks a usable text layer, still create a page-level PDF++ link. Do not leave the locator unlinked; briefly disclose degraded precision when material.
8. Keep the report's `source` property as a normal PDF wikilink without a page fragment.

## Workflow

1. **Inspect inputs**
   - Confirm the PDF exists and count pages.
   - If the output file exists, read it first. Overwrite only when the user clearly requested replacement/update; otherwise ask before replacing substantial content.
2. **Extract and map the paper**
   - Extract full text with page boundaries.
   - Identify title, abstract, sections, methods, algorithms, figures, tables, limitations, appendix, metrics, datasets, and main results.
   - Build a locator map for every page, line, figure, table, and equation cited in the report. Record the first target page and, when selectable, validated PDF.js selection indices.
   - Build a visual inventory that labels each figure/table as task concept, model/method framework, core result, ablation, case study, or supplementary.
   - Render or visually inspect pages containing important figures/tables when layout matters.
3. **Analyze with paper-only evidence**
   - Reconstruct the paper's research question and problem framing.
   - Identify claimed contributions and the mechanism by which the method is supposed to work.
   - Decompose the method into modules and explain each module's inputs, operations, outputs, training/inference behavior, and role in the whole framework.
   - Summarize experiments: datasets, baselines, metrics, main results, ablations, efficiency, limitations, and robustness checks.
   - Include internal inconsistencies or overclaims if visible from the paper itself.
4. **Handle figures/tables**
   - Meet the following visual baseline unless the user explicitly requests a text-only report:
     1. **Task concept figure — required when present.** Show what task/problem the paper proposes or studies. Prefer the paper's own teaser, task overview, problem setup, or conceptual figure.
     2. **Model/method framework figure — required when present.** Include the paper's architecture, pipeline, or method overview if one exists.
     3. **Core result figure/table — select 0–1.** Prefer exactly one result visual that best supports the main conclusion. Use zero only when the paper has no suitable result visual or textual/table evidence is clearly more faithful; do not stack several similar result plots.
     4. **Necessary explanatory figures — include as needed.** Add only figures that materially clarify an algorithm, qualitative phenomenon, key ablation, or difficult spatial/temporal relationship. Avoid decorative or redundant visuals.
   - Place each visual near the first passage that explains it.
   - Crop original figures/tables tightly while retaining panel labels, axes, legends, and information needed for interpretation; avoid full-page screenshots.
   - Upload every local crop with `picgo-image-upload` before editing the report, then insert only the returned Markdown.
   - Caption each inserted original with its visual role and a PDF++-linked source location, for example: `任务概念图｜[[folder/paper.pdf#page=2&selection=...|p.2 Figure 1]]`.
   - Label every reconstructed or agent-created diagram clearly so readers do not mistake it for a paper figure.
   - If a required upload fails, retry only when a safe, obvious correction is available. Otherwise omit the broken insertion, state which required visual is missing, and never insert a local path or placeholder URL.
5. **Write the Obsidian report**
   - Add frontmatter with at least `title`, `date`, `tags`, `source`, and `scope`.
   - Use a clear Chinese structure unless the user requests another language.
   - Use Obsidian wikilinks for local files, e.g. `[[folder/paper.pdf]]`.
   - Render all evidence pointers as PDF++ wikilinks using the rules above; do not emit bare `p.`, `pp.`, `L.`, `Figure`, `Table`, or `Eq.` locators.
   - Prefer concise callouts for scope, key insight, warnings, and review concerns.
   - Use standardized heading numbering (e.g., 1., 1.1., 2., 2.1., 2.2., etc.).

## Default report sections

Include these sections unless the user requests a different structure:

1. `摘要` — what this paper mainly do
2. `研究背景` — why the topic matters and what pain points exist
3. `研究贡献` — what problem the paper solves and its advantages
4. `研究原理` — conceptual and technical rationale
5. `研究方法` — overall framework and structure
6. `方法细节` — detailed module-by-module decomposition
7. `实验总览` — datasets, baselines, metrics, results, ablations, efficiency
8. `论文结论与适用边界`
9. `最终总结`

## Verification checklist

Before responding to the user:

- The output `.md` file exists at the requested path.
- The report states that it is based only on the provided PDF when applicable.
- All local vault references use wikilinks.
- Inserted images use successful PicGo Markdown URLs, not temporary or local paths.
- Every page, line, figure, table, and equation pointer is a PDF++ wikilink; no bare evidence locator remains after removing wikilink contents for inspection.
- Every PDF link resolves to the source PDF, every `page` value is in range, and every `selection` tuple is within the corresponding PDF.js text-content item and offset bounds.
- Every multi-line or multi-page citation targets only its first line or first page while preserving the full range in display text.
- At least one page link and one selection link have been opened in Obsidian to verify navigation. When the PDF was already open, verify PDF++ reused the existing PDF tab.
- The paper's task concept figure is included when one exists; no task concept visual is required when the paper has none.
- The paper's model/method framework figure is included when one exists.
- The report contains no more than one selected core result figure/table.
- Every inserted visual has a role and source location; every reconstruction is labeled as non-original.
- No external sources were used unless explicitly requested.
- Temporary extraction/rendering files are removed unless they are final requested assets.
