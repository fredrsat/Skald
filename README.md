# SKALD -- Prompt Builder

Interactive, browser-based tool for building optimized LLM prompts. Single HTML file, no backend, no dependencies, no build step.

Open `prompt-builder.html` in any browser and start building.

## Features

**Guided wizard** -- 9-step flow from role definition to final prompt, with model-specific tips at each step.

**18 model profiles** -- Claude (Opus 4.7, Sonnet 4.6, Haiku 4.5), GPT-5.5/5.5 Thinking, Gemini 3.1 Pro/Flash-Lite, Grok 4, Llama 4 Maverick/Scout, Mistral Large 3/Small 4/Codestral, DeepSeek V4/R1, Qwen 3.6, Phi-4 Reasoning, and custom. Each profile controls prompt structure (XML tags, Markdown, plain text), best practice hints, and context window info.

**10 templates** -- Pre-configured starting points for common use cases:
- Code Review, Text Rewrite, Summarization
- Data Extraction, Q&A over Documents, Classification
- Email Drafting, Creative Writing, Translation
- API Assistant / Chatbot

**Writing style corrections** -- Checkboxes to counter known LLM writing quirks:
- No em dashes, no AI buzzwords ("delve", "leverage", "robust"...)
- No sycophantic openers, no hedging, no filler phrases
- Plain words, varied sentence rhythm, prose over bullet lists

**Constraints and enhancements** -- Research-backed checklist items for accuracy (ground sources, allow "I don't know"), behavior (no follow-up questions, honesty over agreement), and security (prompt injection delimiters, system prompt protection).

**JSON Schema builder** -- Visual field editor that generates JSON Schema (draft-07) and embeds it in the prompt. Export as `.json`.

**10-language output** -- Prompt structural text, checklist items, and template content translated to English, Norwegian, Spanish, French, German, Portuguese, Chinese, Japanese, and Korean. Switching language updates everything live.

**Two modes** -- Chat mode (single combined prompt for browser UIs) and API mode (separate system/user messages with input placeholder).

**Copy & Open** -- Copies the finished prompt and opens the selected model's web interface (Claude.ai, ChatGPT, Gemini, Grok, DeepSeek, Mistral) in a new tab. Just paste.

**Live preview** -- Real-time prompt preview with character/token estimate, section copy, and full export as `.txt`.

## Usage

```
# Just open the file
open prompt-builder.html

# Or serve it
python3 -m http.server 8000
```

No internet required. Works fully offline via `file://` protocol.

## Architecture

Everything lives in a single `prompt-builder.html` file:

```
prompt-builder.html
  <style>     CSS with light theme, custom properties
  <body>      Intro page, wizard steps, live preview
  <script>    Model profiles, wizard logic, prompt builders,
              schema builder, templates, translations, export
```

State is a vanilla JS object. No frameworks, no npm, no build tools.

## License

MIT
