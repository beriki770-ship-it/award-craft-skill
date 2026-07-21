# award-craft

A Claude Code skill for building websites at Awwwards Site-of-the-Day level — that also pass the accessibility and performance bar most winners skip.

## Where this comes from

In July 2026 we dissected six Site-of-the-Day winners (Lacoste Polo Factory, IZANAMI, CoffeeTech, RISK, Lama Lama, Hiroto Sato's portfolio), pulled the official jury criteria and the developer-jury sub-scores behind each win, and found the pattern: winners average 9+ on animation craft, ~6 on accessibility, ~7.1 on usability. The skill turns that into a working strategy — match their motion, beat them where they don't compete.

Proof it works: **[digital.wildmoments.at/wow](https://digital.wildmoments.at/wow/)** — a page built with this playbook that demonstrates every technique it teaches and measures itself live in your browser. LCP ~0.2s, CLS 0.000, 60fps, keyboard-navigable, real reduced-motion build, 500KB total, no framework.

## What's inside

- The jury math and the open-flank strategy
- What every winner structurally shares (one signature mechanic, two-color palettes, designed preloaders)
- Technique recipes: the one-clock scroll/shader pattern, CLS-free kinetic typography, guarded lazy WebGL, scrub-linked reveals, compositor discipline
- The 2026 cliché list — what to stop shipping
- Eight build rules and a ship gate

## Install

Copy the folder into your Claude Code skills directory:

```
~/.claude/skills/award-craft/SKILL.md
```

Claude picks it up automatically when you ask for award-grade web work.

## License

MIT. Built by [Wild Digital Moments](https://digital.wildmoments.at) with Claude.
