---
name: slides
description: >
  [DESIGN] Thiết kế & UI/UX | Tạo slide deck HTML chuyên nghiệp với layout đa dạng, copywriting formula và chiến lược trình bày hiệu quả. Từ khóa: slide, presentation, tạo slide, HTML slide, slide deck, copywriting slide, layout slide, slide strategy.
---
<!-- SKILL METADATA -->
<!-- Category: [DESIGN] Thiết kế & UI/UX -->
<!-- Purpose: Tạo slide deck HTML chuyên nghiệp với layout đa dạng, copywriting formula và chiến lược trình bày hiệu quả. -->
<!-- Keywords: slide, presentation, tạo slide, HTML slide, slide deck, copywriting slide, layout slide, slide strategy -->

# Slides

Strategic HTML presentation design with data visualization.

## When to Use

- Marketing presentations and pitch decks
- Data-driven slides with Chart.js
- Strategic slide design with layout patterns
- Copywriting-optimized presentation content

## Subcommands

| Subcommand | Description | Reference |
|------------|-------------|-----------|
| `create` | Create strategic presentation slides | `references/create.md` |

## References (Knowledge Base)

| Topic | File |
|-------|------|
| Layout Patterns | `references/layout-patterns.md` |
| HTML Template | `references/html-template.md` |
| Copywriting Formulas | `references/copywriting-formulas.md` |
| Slide Strategies | `references/slide-strategies.md` |

## Routing

1. Parse subcommand from `$ARGUMENTS` (first word)
2. Load corresponding `references/{subcommand}.md`
3. Execute with remaining arguments
