# Create Slides Skill for Claude Code

A [Claude Code](https://docs.anthropic.com/en/docs/claude-code) skill that creates presentation slide decks using the [slides.johnsonlee.io](https://slides.johnsonlee.io) framework -- a lightweight Reveal.js wrapper where you write slides in Markdown. Trigger with `/create-slides` or just say "create slides", "做个 slides", or "写个 PPT". Requires the Claude Code CLI and gh (GitHub CLI).

## What's Included

- **SKILL.md** -- The skill definition that drives the `/create-slides` command. It walks through the full workflow: aligning on topic and outline, scaffolding a GitHub repo with `index.html` and `src/{lang}/` directory structure, writing Markdown slide content chapter by chapter, reviewing with the user, and deploying via GitHub Pages. The skill includes framework reference (auto-layout rules, slide separators, directives), content templates (cover, chapter title, summary, think-about-it), and best practices for presentation style.

## Installation

Clone this repository:

```sh
git clone https://github.com/ai-awesome/create-slides.git
```

Then symlink the skill into your Claude global config directory:

```sh
mkdir -p ~/.claude/skills/create-slides
ln -sf "$(pwd)/create-slides/SKILL.md" ~/.claude/skills/create-slides/SKILL.md
```

Or add directly as a submodule in your dotfiles:

```sh
git submodule add https://github.com/ai-awesome/create-slides.git ~/.claude/skills/create-slides
```

## Customization

The skill is defined entirely in SKILL.md. Edit it to fit your workflow -- adjust the HTML template, change the deployment target, add custom CSS overrides, or modify the content writing guidelines.

## Contributing

1. Fork this repository and create a feature branch.
2. Make your changes and ensure they are consistent with the existing style.
3. Submit a pull request with a clear description of what you changed and why.
