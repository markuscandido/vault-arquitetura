# LLM Wiki Schema & Instructions

This workspace follows the **LLM Wiki pattern**. It is a persistent, compounding knowledge base built and maintained by the LLM (Gemini) based on raw sources.

## Core Structure

- `raw/`: Immutable source documents (articles, papers, notes).
- `wiki/`: LLM-generated markdown files.
  - `index.md`: Content-oriented catalog of all pages.
  - `log.md`: Chronological log of operations (ingests, queries, maintenance).
  - Entity, Concept, and Topic pages: Interlinked markdown files.

## Mandatory Workflows

### 0. Language Policy
- **Language**: All content in the `wiki/` directory MUST be maintained in **Brazilian Portuguese (PT-BR)**.
- **Technical Terms**: Standard industry terms (e.g., "Unit of Work", "Active Record") should be kept in English when they are the primary name of a pattern, but descriptions and headings must be in Portuguese.

### 1. Ingest
When a new source is added to `raw/`:
1. **Read & Extract**: Analyze the source for key information, entities, and concepts.
2. **Summarize**: Create a new page in `wiki/sources/` (or similar) summarizing the source.
3. **Integrate**: 
    - Identify relevant existing pages in `wiki/`.
    - Update them with new information, noting contradictions or reinforcements.
    - Create new entity/concept pages if they don't exist.
4. **Cross-reference**: Ensure all related pages are interlinked.
5. **Log**: Add an entry to `wiki/log.md` with the format `## [YYYY-MM-DD] ingest | Source Title`.
6. **Index**: Update `wiki/index.md` with the new page(s).

### 2. Query
When the user asks a question:
1. **Search**: Consult `wiki/index.md` and use `grep_search` to find relevant information.
2. **Synthesize**: Generate an answer based on the wiki and raw sources.
3. **Persistence**: If the answer provides a valuable new synthesis or connection, ask the user if it should be saved as a new wiki page.
4. **Log**: Add an entry to `wiki/log.md` with the format `## [YYYY-MM-DD] query | Question summary`.

### 3. Maintenance (Lint)
Periodically or when requested:
1. **Consistency**: Check for contradictions between pages.
2. **Connectivity**: Find orphan pages or missing links.
3. **Updates**: Suggest new topics to explore or gaps to fill.
4. **Log**: Add an entry to `wiki/log.md` with the format `## [YYYY-MM-DD] maintenance | Task summary`.

## Conventions

- **Markdown**: Use standard Markdown.
- **Interlinking**: Use `[[Page Name]]` style links or standard `[Link Text](path/to/file.md)`. Since we are in a CLI/Filesystem context, standard relative paths are preferred for tool-readability, but `[[link]]` is good for Obsidian compatibility.
- **YAML Frontmatter**: Include metadata (tags, sources, date) at the top of every wiki page.
- **No Deletions**: Do not delete information from the wiki unless it's a correction of an LLM error. Prefer appending/revising to show evolution of knowledge.

## Goal
The wiki is a codebase; the LLM is the programmer; Obsidian is the IDE. The goal is to minimize the human maintenance burden while maximizing knowledge accumulation.
