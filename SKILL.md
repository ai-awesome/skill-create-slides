---
name: create-slides
description: >
  Create presentations using the slides.johnsonlee.io framework (Reveal.js + Markdown).
  Trigger when user mentions "create slides", "presentation", "make a deck", "slide deck",
  "做个 slides", "做个演讲", "写个 PPT", "做个分享".
  Also applies when the user provides a topic or outline and wants a complete slide deck.
---

# Slides Creator

Create presentations based on the [slides.johnsonlee.io](https://slides.johnsonlee.io) framework. It is a lightweight Reveal.js wrapper that uses Markdown for slide content.

## Workflow

1. **Clarify topic and audience** -- Ask the user for the **target audience** first (e.g. beginners, domain experts, general public, kids). The audience determines the presentation style. Then align on topic, language (en/zh/bilingual), and chapter structure
2. **Project Setup** -- Initialize a GitHub repo, generate `index.html` and directory structure
3. **Write content** -- Generate Markdown slides chapter by chapter
4. **User review** -- Let the user review and revise based on feedback
5. **Deploy** -- Push to GitHub and publish via GitHub Pages

## Framework Overview

### Usage

Presentations are static HTML sites that reference the framework via CDN:

```html
<head>
  <link rel="stylesheet" href="https://slides.johnsonlee.io/slides.css">
</head>
<body>
  <script src="https://slides.johnsonlee.io/slides.js"></script>
  <script>
    Slides.init({
      title: 'Presentation Title',
      chapters: ['cover', 'chapter-1', 'chapter-2'],
      langs: { en: 'EN', zh: '中文' }
    });
  </script>
</body>
```

### Configuration

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `title` | `string` | Yes | Page title, used in the `<title>` tag |
| `chapters` | `string[]` | Yes | Markdown file names (without `.md` extension) |
| `langs` | `{ code: label }` | No | Language mapping, e.g. `{ en: 'EN', zh: '中文' }` |
| `reveal` | `object` | No | Override Reveal.js initialization options |

### Directory Structure

```
project-root/
  index.html              # Main page containing the Slides.init() config
  src/
    en/                    # English content
      cover.md
      chapter-1.md
      chapter-2.md
    zh/                    # Chinese content (if bilingual)
      cover.md
      chapter-1.md
      chapter-2.md
  assets/                  # Language-independent image assets
    diagram.svg
```

## Markdown Conventions

### Slide Separators

- `---` (with a blank line before and after): Horizontal separator within a chapter, creates a new slide
- `----` (with a blank line before and after): Vertical separator, creates a sub-slide

```markdown
## Slide 1 Title

Content here

----

## Slide 1.1 (vertical sub-slide)

More content

----

## Slide 1.2

Even more content
```

### Slide Directives

Use HTML comments at the beginning of a slide to control layout:

```markdown
<!-- .slide: class="center" -->

## Centered Title

*Subtitle text*
```

### Auto-Layout Rules

The framework automatically detects slide content and applies the best layout -- **no manual configuration needed**:

1. **Center layout** -- Title + at most one line of text (no lists/tables/images) -> auto-centered. Can also be triggered manually with `<!-- .slide: class="center" -->`
2. **Stretched list** -- Title + a single list (<=8 items) -> list stretches to fill space, items evenly distributed
3. **Auto columns** -- Title + a single list (>8 items) -> automatically split into `ceil(N/8)` columns
4. **Table** -- Title + table -> table fills available width below the title
5. **Image** -- Title + image -> image centered in remaining space
6. **Multiple content blocks** -- Title + multiple content blocks -> spacing automatically added from the second block onward

### Cover Slide Template

The cover is typically centered and contains the title, subtitle, and author info:

```markdown
<!-- .slide: class="center" -->

# Presentation Title

### Subtitle or Description

&nbsp;

Author Name | Year
```

### Chapter Title Template

The first slide of each chapter is typically a centered chapter title:

```markdown
<!-- .slide: class="center" -->

## Chapter N: Chapter Title

*Brief description or question*
```

### Content Slide Best Practices

- **Focus on one point per slide** -- do not cram too much content
- **Keep list items to 8 or fewer** -- more than 8 triggers auto-columns (split across multiple slides if you prefer no columns)
- **Use sub-slides** (`----` separator) to group related content together
- **Use tables** for comparisons or structured data
- **Use blockquotes** (`>`) to highlight key takeaways
- **Code blocks** are automatically syntax-highlighted and line-numbered (except `.text` class)
- **Images** use standard Markdown syntax: `![alt](path/to/image.svg)`

### LaTeX Math

Inline and block math expressions are supported:

```markdown
Inline math: $E = mc^2$

Block math:

$$
L = -\sum_{i} p(x_i) \log q(x_i)
$$
```

### Images

- **Language-independent images** go in the `assets/` directory: `![Diagram](assets/diagram.svg)`
- **Language-specific images** go in `src/{lang}/images/`, with each language's Markdown referencing its own path
- Prefer **SVG** format for clarity and scalability

## Project Setup

### Step 1: Create a GitHub Repository

Skip this step if the repository already exists.

```bash
# Create a repo (public, for GitHub Pages)
gh repo create {owner}/{repo-name} --public --clone
cd {repo-name}
```

### Step 2: Generate index.html

Generate `index.html` based on user requirements, including:
- Proper `<meta>` tags (charset, viewport, description, author)
- Open Graph tags (og:title, og:description, og:image, og:url)
- `<link>` referencing `slides.css`
- `<script>` referencing `slides.js` and calling `Slides.init()`

**Reference template** (bilingual presentation):

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Presentation Title</title>
    <meta name="description" content="A brief description of the presentation.">
    <meta name="author" content="Johnson Lee">
    <meta property="og:type" content="website">
    <meta property="og:title" content="Presentation Title">
    <meta property="og:description" content="A brief description.">
    <link rel="stylesheet" href="https://slides.johnsonlee.io/slides.css">
  </head>
  <body>
    <script src="https://slides.johnsonlee.io/slides.js"></script>
    <script>
      Slides.init({
        title: 'Presentation Title',
        chapters: ['cover', 'chapter-1', 'chapter-2', 'references'],
        langs: { en: 'EN', zh: '中文' }
      });
    </script>
  </body>
</html>
```

### Step 3: Create Directories and Content Files

```bash
mkdir -p src/en src/zh
```

Write Markdown files chapter by chapter.

### Step 4: Configure GitHub Pages

```bash
# Push code
git add -A && git commit -m "init: slides project" && git push -u origin main

# Enable GitHub Pages (main branch, root directory)
gh api repos/{owner}/{repo-name}/pages --method POST --field source='{"branch":"main","path":"/"}'
```

After deploying, ask the user whether they want to use a custom domain. If yes, ask for the domain name, then:

1. Create a `CNAME` file:

```bash
echo "{custom-domain}" > CNAME
```

2. Add `og:url` to `index.html`:

```html
<meta property="og:url" content="https://{custom-domain}/">
```

3. Commit and push the changes.

## Content Writing Guidelines

### Presentation Style

Ask the user which style fits their audience. Four built-in styles are available:

#### 1. Executive Briefing -- reporting to investors, executives, or management

**Narrative:** Situation → Problem → Solution → Impact → Ask

- **Lead with conclusions** -- State the result or recommendation first, then provide supporting evidence
- **Quantify everything** -- Use concrete metrics, KPIs, ROI, and timelines instead of vague descriptions
- **Keep slides minimal** -- One key message per slide, large fonts, no clutter
- **Answer "so what?" and "what do you need from me?"** -- Decision-makers care about outcomes and next steps
- **Anticipate objections** -- Address risks and mitigations proactively
- **Time-efficient** -- Target 10-20 slides for a 15-30 minute session; leave time for Q&A

#### 2. Peer Sharing -- presenting to fellow practitioners in the same domain

**Narrative:** Context → Approach → Findings → Lessons Learned → Open Questions

- **Skip the basics** -- Assume shared vocabulary and foundational knowledge
- **Focus on insights and trade-offs** -- What did you learn? What surprised you? What would you do differently?
- **Show the details** -- Include architecture diagrams, benchmarks, code snippets, data tables
- **Be honest about limitations** -- Peers respect intellectual honesty over polished narratives
- **Encourage discussion** -- Leave open questions and decision points for the audience to weigh in
- **Moderate density** -- Target 15-30 slides for a 30-45 minute session

#### 3. Public Explainer -- presenting to a general audience unfamiliar with the topic

**Narrative:** Hook → "Why should I care?" → Build understanding step by step → "Now you get it" → Takeaway

- **Make complex ideas accessible** -- Use simple analogies and everyday metaphors to explain concepts
- **Build incrementally** -- Introduce only one new concept per slide; connect each to the previous one
- **Be engaging** -- Use rhetorical questions, relatable scenarios, and storytelling to maintain interest
- **Be concrete** -- Use real-world examples instead of abstract descriptions
- **Control pacing** -- Follow complex content with simple summary or transition slides
- **Moderate length** -- Target 20-40 slides for a 30-60 minute session

#### 4. Educational -- teaching students or kids who are learning the subject

**Narrative:** Wonder → Explore → Discover → Practice → Reflect

- **Start from zero** -- Assume no prior knowledge; define every term before using it
- **Use vivid analogies** -- Map abstract concepts to things the audience already knows (games, daily life, school)
- **One idea per slide** -- Never introduce two concepts at once
- **Add "Think About It" questions** -- End each chapter with open-ended questions that connect to real life
- **Include chapter summaries** -- Recap each chapter's key ideas in a table before moving on
- **Use progressive disclosure** -- Pose a question first, let the audience think, then reveal the answer on the next slide
- **Keep it fun** -- Vary slide types (lists, tables, images, quotes) to maintain attention
- **Generous length** -- Target 30-60 slides for a 45-90 minute session

### Chapter Structure

The chapter structure varies by presentation style:

#### Executive Briefing

1. **cover** -- Title, presenter name, date
2. **executive-summary** -- 1-2 slides with the key conclusion and ask up front
3. **chapter-1 ~ chapter-N** -- Supporting evidence, each chapter = one argument
4. **next-steps** -- Clear action items, owners, and timeline

No chapter summaries needed -- the executive summary at the front serves that purpose. No "Think About It" slides -- end with a concrete ask or decision point.

#### Peer Sharing

1. **cover** -- Title, presenter name, affiliation
2. **context** -- Problem space and motivation (brief, peers know the domain)
3. **chapter-1 ~ chapter-N** -- Technical deep-dives, each chapter = one topic area
4. **takeaways** -- Key lessons, surprises, what you'd do differently
5. **discussion** -- Open questions for the audience

Chapter summaries are optional -- use them only for long or dense chapters. End chapters with open questions or decision points to spark discussion.

#### Public Explainer

1. **cover** -- Title, subtitle, author
2. **outline** -- What we'll cover and why it matters (optional)
3. **chapter-1 ~ chapter-N** -- Main content, each chapter = one concept
4. **references** -- Further reading (optional)

Each chapter should end with a brief summary slide recapping key ideas:

```markdown
## Chapter N Summary

| Topic | Key Idea |
|-------|----------|
| Concept A | One-sentence explanation |
| Concept B | One-sentence explanation |
```

#### Educational

1. **cover** -- Title, subtitle, author
2. **preface** -- Why this topic matters, what you'll learn
3. **chapter-1 ~ chapter-N** -- Main content, each chapter = one concept built on the previous
4. **references** -- Further reading and resources

Each chapter must end with **both** a summary table and a "Think About It" slide:

```markdown
## Chapter N Summary

| Topic | Key Idea |
|-------|----------|
| Concept A | One-sentence explanation |
| Concept B | One-sentence explanation |

----

## Think About It

- Question that connects to the content?
- Question that encourages deeper thinking?
- Question that relates to real life?
- Question that bridges to the next chapter?
```

## Generating Slides from Conversation

If the user says "turn this into slides" after discussing a topic, the process is:

1. Review the conversation and extract core points and logical structure
2. Design chapter divisions and the number of slides per chapter
3. Reorganize the content in a presentation style (do not simply copy the conversation)
4. Add transition and summary slides to ensure a smooth flow
5. Follow the normal process: let the user confirm, then push
