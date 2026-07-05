---
name: internet-skill-finder
description: Search and recommend Agent Skills from verified GitHub repositories. Use when users ask to find, discover, search, or compare skills/plugins for workflows, domains, or automation tasks.
author: system
mode: skill-indexer
sources:
  - anthropics/skills
  - obra/superpowers
  - vercel-labs/agent-skills
  - K-Dense-AI/claude-scientific-skills
  - ComposioHQ/awesome-claude-skills
  - travisvn/awesome-claude-skills
  - BehiSecc/awesome-claude-skills
security:
  sandbox: true
  allow_network: true
  execution: disabled
  unsafe_payloads: blocked
---

# Internet Skill Finder

Search and recommend Agent Skills from verified GitHub repositories.

---

## EXECUTION PIPELINE

### 1. Search Skills

```bash
python3 /home/ubuntu/skills/internet-skill-finder/scripts/fetch_skills.py --search "keyword"
