# Obsidian Paper Reading Skill

`obs-read-paper` is an agent skill for deeply reading research papers and generating structured Markdown reports inside an Obsidian vault.

The generated report includes source-grounded analysis, selected figures/tables, and clickable PDF++ links back to the exact pages and text selections in the original paper.

## Installation

Copy this skill into your agent's skills directory.

### Codex

```
mkdir -p ~/.codex/skills
cp -R obs-read-paper ~/.codex/skills/
```

### Claude Code

```
mkdir -p ~/.claude/skills
cp -R obs-read-paper ~/.claude/skills/
```

Restart the agent after installation so the skill can be discovered.

## Prerequisites

### Tools

- **Obsidian** — the target vault and Markdown environment.
- **PDF++** — required for precise page/text-selection links such as `#page=4&selection=...`.
- **Obsidian CLI** — used to inspect the vault, resolve links, and verify PDF++ navigation.
- **PicGo** — used to upload cropped figures and tables.

For Obsidian CLI, use Obsidian 1.12.7+ and enable:

```
Settings → General → Command line interface
```

For PDF++, install and enable the `PDF++` community plugin in Obsidian.

For PicGo, configure an image host and enable **PicGo Server**. The default server address is:

```
http://127.0.0.1:36677
```

### Required skills

| Skill                | Purpose                                                      |
| -------------------- | ------------------------------------------------------------ |
| `pdf`                | PDF text extraction, page rendering, figures and tables      |
| `deep-research`      | Source-grounded research report structure                    |
| `obsidian-markdown`  | Obsidian Markdown, wikilinks and callouts                    |
| `obsidian-cli`       | Vault operations and PDF++ navigation verification           |
| `picgo-image-upload` | Upload extracted figures/tables and return Markdown image syntax |

`obsidian-markdown` and `obsidian-cli` are available from `kepano/obsidian-skills`.

`picgo-image-upload` is available from `Evan02580/picgo-image-upload`.

### Optional skill

- `nature-reader` — used only for full-paper reading, bilingual translation, paragraph-level source maps, or figure/table-aware reader artifacts.

## Configuration

Before use, make sure:

1. The paper PDF is stored inside or accessible from the Obsidian vault.
2. Obsidian is running and `obsidian help` works in the terminal.
3. PDF++ is enabled and can open links containing `#page=` and `&selection=`.
4. PicGo Server is running and `picgo-image-upload` can successfully upload an image.
5. All required skills are visible to the agent.

The skill uses **vault-relative paths** for PDFs and Markdown files.

## Usage

Provide both the paper and the output Markdown path:

```
obs 阅读 @papers/example.pdf
输出到 notes/example.md
```

You can also specify a focus:

```
深度阅读 @papers/example.pdf，
重点分析研究问题、方法、实验设计和局限，
保存到 notes/example.md。
仅使用论文自身内容。
```

The resulting note will contain structured paper analysis, selected original figures/tables, and clickable PDF++ evidence links back to the source PDF.