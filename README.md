# Create an LLM Wiki

Step-by-step instructions for creating a personal LLM-maintained knowledge base using Andrej Karpathy's LLM Wiki pattern.

This guide is based on:

- Urvil Joshi's walkthrough: <https://medium.com/@urvvil08/andrej-karpathys-llm-wiki-create-your-own-knowledge-base-8779014accd5>
- Your fork of Karpathy's idea file: <https://gist.github.com/dunkel000/fbcd99a1830dae52aadfa48f911ae3af>
- Karpathy's original gist: <https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f>

## What You Are Building

An LLM Wiki has three layers:

1. `raw/`: immutable source files such as PDFs, articles, meeting notes, transcripts, images, and markdown clips.
2. `wiki/`: LLM-generated markdown pages that summarize, connect, compare, and synthesize the sources.
3. `AGENTS.md` or `CLAUDE.md`: the operating schema that tells the LLM how to maintain the wiki.

The goal is to compile raw knowledge into a persistent, interlinked wiki. Instead of asking an LLM to rediscover context from raw files every time, you let it maintain a structured knowledge base that compounds over time.

## Prerequisites

- An LLM coding agent such as OpenAI Codex, Claude Code, OpenCode, or a similar local agent.
- Obsidian, optional but recommended, for browsing the markdown wiki and graph view.
- A folder on your computer that will become the wiki vault.
- Git, optional but recommended, for version history.

## Step 1: Create the Wiki Folder

Create a new directory for your wiki:

```powershell
mkdir my-llm-wiki
cd my-llm-wiki
mkdir raw
mkdir wiki
```

The starting structure should look like this:

```text
my-llm-wiki/
+-- raw/
+-- wiki/
```

Use `raw/` only for source material. The LLM should read from this folder but should not rewrite or reorganize the original sources.

## Step 2: Start Your LLM Agent in the Folder

Open your preferred agent in the wiki folder.

For OpenAI Codex, start it from inside `my-llm-wiki/`. For Claude Code, do the same and use `CLAUDE.md` instead of `AGENTS.md` in the next step.
For GitHub Copilot, use `.github/copilot-instructions.md`.

## Step 3: Give the Agent the LLM Wiki Idea File

Ask the agent to read your forked gist and instantiate the pattern for your own topic:

```text
Read Andrej Karpathy's LLM Wiki idea file from my fork:
https://gist.github.com/dunkel000/fbcd99a1830dae52aadfa48f911ae3af

Help me set up an LLM Wiki in this directory.

Before creating or editing files, ask me:
- what this wiki will be about
- what kinds of sources I plan to add
- how large I expect the source collection to become
- what page types would be useful

After I answer, create an  `.github/copilot-instructions.md` for Gihub Copilot, then initialize wiki/index.md and wiki/log.md.
```
# After I answer, create an AGENTS.md schema for OpenAI Codex, then initialize wiki/index.md and wiki/log.md.

If the agent cannot access the gist URL, paste the full contents of the gist into the chat after the prompt.

## Step 4: Answer the Agent's Setup Questions

Give specific answers. For example:

```text
This wiki is about AI research and software philosophy.
I will feed it essays, blog posts, papers, and notes from people like Rich Sutton, Andrej Karpathy, and other AI researchers.
I expect 50 to 100 sources over time.
Useful page types are source summaries, concept pages, author pages, comparison pages, and synthesis notes.
```

The agent should then create:

```text
my-llm-wiki/
+-- AGENTS.md
+-- raw/
+-- wiki/
    +-- index.md
    +-- log.md
```

`AGENTS.md` is the schema. It should define page conventions, ingest rules, citation style, linking rules, logging format, and maintenance workflows.

## Step 5: Add Your First Source

Put one source file into `raw/`. Start with a small, high-quality source so you can inspect the result.

Example:

```text
my-llm-wiki/
+-- raw/
|   +-- bitter-lesson.md
+-- wiki/
```

Then ask the agent:

```text
Ingest raw/bitter-lesson.md.

Follow AGENTS.md. Create a source summary, update wiki/index.md, append to wiki/log.md, and create or update any relevant concept or author pages. Use Obsidian-style [[wikilinks]] where useful.
```

After ingestion, the wiki may look like:

```text
wiki/
+-- index.md
+-- log.md
+-- sources/
|   +-- bitter-lesson.md
+-- concepts/
|   +-- search.md
|   +-- learning.md
|   +-- general-methods.md
+-- people/
    +-- rich-sutton.md
```

Review the generated pages. Correct anything that is wrong before ingesting more sources.

## Step 6: Ingest Sources One at a Time

For each new source:

1. Add the file to `raw/`.
2. Ask the agent to ingest that exact file.
3. Review the changed wiki pages.
4. Commit the changes if you are using Git.

Example prompt:

```text
Ingest raw/software-2-0.md.

Before writing, read wiki/index.md and related existing pages. Integrate this source into the current wiki instead of creating duplicate concepts. Update summaries, cross-links, contradictions, and wiki/log.md.
```

The value of the system comes from integration. The agent should update existing concept pages when a new source changes the synthesis.

## Step 7: Ask Questions Against the Wiki

Once you have a few sources ingested, ask synthesis questions:

```text
Using only the wiki layer unless raw sources are needed for verification, explain how Sutton's Bitter Lesson and Karpathy's Software 2.0 agree about the future of software, and where they differ.

If the answer is useful, save it as a new synthesis page and update wiki/index.md and wiki/log.md.
```

Good answers can become permanent wiki pages instead of disappearing into chat history.

## Step 8: Lint the Wiki Periodically

Ask the agent to audit the wiki:

```text
Lint the wiki.

Look for contradictions, stale claims, orphan pages, missing backlinks, duplicate concepts, weak citations, and important concepts that are mentioned but do not have their own page. Do not modify files yet. First give me a proposed maintenance plan.
```

After reviewing the plan, ask it to apply the selected fixes:

```text
Apply the approved lint fixes. Update wiki/log.md with a lint entry.
```

## Step 9: Open the Wiki in Obsidian

Open Obsidian and create a vault from the `my-llm-wiki/` folder.

Recommended Obsidian settings:

- Use `wiki/` as the main browsing area.
- Keep attachments in a stable folder such as `raw/assets/`.
- Use graph view to inspect page relationships.
- Consider Dataview later if your pages use YAML frontmatter.

## Recommended Operating Rules

- Keep `raw/` immutable. If a source changes, add a new version or note the update.
- Keep `wiki/log.md` append-only.
- Make the agent read `AGENTS.md` before every ingest, query, or lint task.
- Prefer one-source-at-a-time ingestion until the workflow is reliable.
- Spot-check generated summaries against raw sources.
- Use Git commits as checkpoints.
- Let the LLM maintain the wiki, but keep yourself responsible for source quality and factual review.

## Suggested Git Workflow

Initialize version history:

```powershell
git init
git add .
git commit -m "Initialize LLM wiki"
```

After each ingest or lint pass:

```powershell
git status
git diff
git add AGENTS.md wiki raw
git commit -m "Ingest source-name"
```

## Common Prompts

Ingest a source:

```text
Ingest raw/source-file.md according to AGENTS.md. Update existing pages before creating new ones. Add citations, wikilinks, index entries, and a log entry.
```

Ask a question:

```text
Answer this using the wiki first. Cite the wiki pages you used. If raw source verification is needed, say which source you checked.
```

Save a useful answer:

```text
Save this answer as a synthesis page in wiki/synthesis/. Add backlinks, update wiki/index.md, and append to wiki/log.md.
```

Run maintenance:

```text
Lint the wiki for contradictions, stale claims, orphan pages, missing backlinks, duplicate concepts, and missing source coverage. Propose changes before editing.
```

## Notes

This pattern works best for bounded, curated knowledge collections where synthesis matters more than exact retrieval over millions of documents. It is not a replacement for RAG in every setting. The main tradeoff is that summarized knowledge can drift from the original source if you do not review and lint it regularly.
