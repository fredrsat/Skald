# SKALD -- Prompt Builder

Interactive, browser-based tool for building optimized LLM prompts. Single HTML file, no backend, no dependencies, no build step.

Open `prompt-builder.html` in any browser and start building.

## Features

**Guided wizard** -- 9-step flow from role definition to final prompt, with model-specific tips at each step.

**13 model profiles** -- Claude (Opus, Sonnet, Haiku), GPT-4.1, GPT-4o, Gemini 2.0 (Flash, Pro), Llama 3, Mistral, Mixtral, and custom. Each profile controls prompt structure (XML tags, Markdown, plain text), best practice hints, and context window info.

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

**10-language output** -- Prompt structural text and checklist items translated to English, Norwegian, Spanish, French, German, Portuguese, Chinese, Japanese, and Korean.

**Two modes** -- Chat mode (single combined prompt for browser UIs) and API mode (separate system/user messages).

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
