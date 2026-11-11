# AI Agent Guidelines & Repository Context

## Repository Overview

This repository stores standalone, plain-text Content Blocker subscription lists designed for the **Stay** browser extension on iOS, iPadOS, and macOS Safari.

- **Primary Directory**: [`lists/`](./lists)
- **Target Runtime**: Stay `ContentFilterTypeSubscribe` engine (compiles plain-text lists via `HTTPS GET` into Safari Content Blocker JSON rules for `Stay Content Subscribe`).
- **Scope & Discretion**: Documentation files ([`README.md`](./README.md) and [`AGENTS.md`](./AGENTS.md)) must describe repository architecture, Stay compatibility rules, and workflows generically without highlighting or indexing specific target websites. Each list file in [`lists/`](./lists) documents its own scope directly.

---

## File Placement, Naming & Formatting

1. **Location & Naming**:
   - Place all subscribable rulesets in [`lists/`](./lists) using lowercase kebab-case filenames ending in `.txt` (e.g., `lists/<scope-name>.txt`).
   - Partition lists cleanly by domain family or purpose so each list can be subscribed to and toggled independently in Stay.
2. **Encoding & Line Endings**:
   - Use **UTF-8** encoding with **LF (`\n`)** line endings (`.gitattributes` enforces `eol=lf`).
   - Every rule line must be strictly shorter than **8192 characters**.
3. **Required List Header**:
   ```adblock
   [Adblock Plus 2.0]
   ! Title: Descriptive List Title
   ! Version: 1.0.0
   ! Expires: 4 days
   ```
   Stay's parser recognizes `Homepage`, `Title`, `Expires`, `Redirect`, and `Version` special comments (`! Key: Value`). Keep `! Expires: <N> days` explicit so Stay refreshes lists predictably.

---

## Stay Content Blocker Compiler Constraints

Stay translates plain-text rules into Apple `SFContentBlockerManager` JSON using its custom AST compiler rather than a full uBlock Origin or desktop Adblock Plus engine. Always respect these verified constraints when authoring or editing lists:

1. **No Implicit Subdomain Wildcards for `||`**:
   - Stay compiles `||` literally to `^https?://` without inserting subdomain matching.
   - A rule like `||example.com/path/` matches `https://example.com/path/...`, **not** `https://cdn.example.com/path/...`.
   - When subdomains must also be covered, explicitly pair apex and wildcard rules:
     ```adblock
     ||example.com/path/
     ||*.example.com/path/
     ```

2. **80-Character Compiled `url-filter` Limit**:
   - Stay silently drops any rule whose compiled regex `url-filter` exceeds **80 characters**.
   - Remember that `||` expands to `^https?://` (10 chars), `.` escapes to `\.` (+1 char each), `*` expands to `.*` (+1 char each), and `^` expands to `[^a-zA-Z0-9_\-.%]` (19 chars).
   - Keep trigger patterns concise so compiled output stays well within 80 characters.

3. **Regex Parentheses Restriction**:
   - `/regex/` patterns containing parentheses `(...)` are rejected by Stay's parser.
   - Instead of alternation groups like `/(foo|bar)\.example\.com/`, write separate flat rules.

4. **Supported vs. Unsupported Rule Syntax**:
   - **Supported**:
     - Network triggers: `||domain^`, `|anchored`, substrings, simple `/regex/`, and standard `$options` (`$script`, `$image`, `$stylesheet`, `$xmlhttprequest`, `$domain=...`, `$third-party`, etc.).
     - Network exceptions: `@@...` (`ignore-previous-rules`).
     - Element hiding: `##selector` and `domain1.com,domain2.com##selector` (`css-display-none`).
   - **Unsupported / Avoid**:
     - Scriptlet injection (`#%#`, `#@%#`), extended/procedural CSS (`#$#`, `#?#`, `#@$#`), HTML filtering (`$$`, `$@$`), and preprocessor directives (`!#if`, `!#endif`) are silently dropped.
     - Cosmetic exceptions (`#@#`) do not emit Safari exception actions in Stay; avoid them.
     - Comma-separated domain prefixes before `##` must be plain hostnames without `*` wildcards.
     - Keep `##` CSS selectors compatible with WebKit Content Blocker rules — a single invalid CSS selector can cause Safari to reject the entire compiled JSON bundle.
     - Unscoped bare wildcards without domain/selector context are ignored.

5. **Shared Extension Rule Budget**:
   - All enabled Stay subscriptions compile into a single `Subscribe.json` payload with a combined ceiling of **149,900** rules. Keep lists focused and free of redundant rules.

---

## Authoring & Verification Workflow

1. **Inspect Before Blocking**:
   - Never guess target domains, request paths, or CSS selectors.
   - Verify live network traffic and DOM structure using browser devtools or inspection scripts first, ensuring shared API endpoints, media manifests, or UI controls are not inadvertently blocked.
2. **Validate Every List Before Committing**:
   - Verify UTF-8 + LF (`\n`) line endings, check that no line uses unsupported syntax (`#%#`, `#$#`, `#?#`, `$$`, `#@#`, `(a|b)` regexes), and confirm compiled `url-filter` lengths remain `<= 80` characters.
3. **Organized Commits**:
   - Group commits cleanly by list update, new ruleset addition, or repository tooling/documentation change using conventional commit messages.
