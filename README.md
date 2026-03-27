# SKALD -- Prompt Builder

Interactive, browser-based tool for building optimized LLM prompts. Single HTML file, no backend, no dependencies, no build step.

Open `prompt-builder.html` in any browser and start building.

## Features

**Guided wizard** -- 9-step flow from role definition to final prompt, with model-specific tips at each step.

**20+ model profiles** -- Claude (Opus, Sonnet, Haiku), GPT-4.1/4o/o3/o4-mini, Gemini 2.5/2.0, Llama 4/3, Mistral/Codestral, DeepSeek V3/R1, Qwen, Phi-4, and custom. Each profile controls prompt structure (XML tags, Markdown, plain text), best practice hints, and context window info.

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

**Copy & Open** -- Copies the finished prompt and opens the selected model's web interface (Claude.ai, ChatGPT, Gemini, DeepSeek, Mistral) in a new tab. Just paste.

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
