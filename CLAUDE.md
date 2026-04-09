# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Thai-Chinese language learning notes project. Thai academic articles are processed into structured study notes and interactive web pages for a Chinese-speaking learner (Traditional Chinese output).

## Workflow

Source article (`.txt`, converted from PDF) → study notes (`_notes.md`) → interactive HTML (`_interactive.html`)

File naming: `L{n} {title}.txt` → `L{n}_notes.md` → `L{n}_interactive.html`

## Notes Format (`_notes.md`)

Three sections in order:

**1. 泰文逐句翻譯** — grouped by 段落 with bilingual headings (`### 段落x：中文 · ภาษาไทย`), each sentence block:
```
**泰文：** [word-segmented Thai, trailing two spaces for <br>]  
**中翻：** [Chinese translation]

重要單字：
- **泰文** [romanization] 中翻 (English) ← 字構/記憶線索
```

**2. 常用例句** — per word:
```
- **word** 中翻
  - 泰：[example sentence]
  - 中：[translation]
```

**3. 近似字整理** — confusable pairs with explanation

## Formatting Rules

- **No markdown tables** — indented bullet lists only
- **Thai sentences must be word-segmented** with double spaces between words: `ลอง  นึกภาพ  โลก`
- **Each `**泰文：**` line must end with two trailing spaces** to produce a `<br>` in preview, keeping Thai and Chinese on separate lines
- Romanization: tone-marked (e.g., `phǎen thîi`, `lam dàp chán`)
- 字構: for words with pure language-of-origin entries (e.g. `來自巴利語`), provide a short mnemonic instead — phonetic association or compound hint, one item only
- All Chinese: Traditional Chinese (繁體中文)
- File has a `## 目錄` TOC at the top with bilingual anchor links

## Repeated Vocab Highlighting (`<mark>`)

Vocab words first defined in an earlier section are wrapped in `<mark>word</mark>` when they reappear in later sections. This renders as yellow highlight in VS Code preview and GitHub.

To regenerate after editing, run:
```js
// node script — reads L1_notes.md, re-applies <mark> tags
// 1. parse ### sections, collect vocab words per section (from "- **word**" lines)
// 2. for each **泰文：** line, wrap tokens matching prior-section vocab in <mark>
// matching: exact (clean === vocabWord) or substring (v.length >= 4 && clean.includes(v))
```

## Interactive HTML (`_interactive.html`)

Single-file, no external dependencies, dark theme. The file embeds all content as JS objects:

- `sections` — array of `{ title, sentences: [{ thai, zh, vocab: [{ thai, roman, zh, en, struct }] }] }`
- `examples` — array of `{ word, zh, thai_ex, zh_ex }`
- `similarWords` — array of `{ w1, r1, m1, w2, r2, m2, note }`

**Five tabs:** 逐句閱讀 · 常用例句 · 近似字 · 單字卡 · 測驗

**Word interaction (逐句閱讀 tab):**
- Thai sentences split on double spaces into `<span class="word">` elements
- Words matched against `sent.vocab` first, then `allVocab` (all vocab across sections)
- Words only in `allVocab` (not current sentence) get class `word-review` (gold color `#e8c547`) — indicates previously-introduced vocab
- Clicking any word plays pronunciation immediately (Google Translate TTS primary, Web Speech API fallback)
- Hovering shows tooltip with full vocab entry (no speak button in tooltip)

**TTS:** `speak(text, el)` — Google Translate TTS at `ttsspeed=0.5`; strips extra spaces before sending

**Content must stay in sync** between `_notes.md` and `_interactive.html` (same sentences, same vocab).
