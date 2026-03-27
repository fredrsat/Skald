# Prompt Builder — Specification
**Version:** 0.1  
**Dato:** 2026-03-27  
**Formål:** Leveringsdokument til Claude Code CLI

---

## 1. Oversikt

Prompt Builder er et interaktivt, nettleserbasert verktøy (enkelt HTML-fil med innebygd JS/CSS) som hjelper brukere å konstruere optimale prompts for ulike LLM-modeller. Verktøyet kombinerer en guided wizard-flyt med live redigering, model-spesifikk best practice og automatisk generering av JSON output schema.

Primærmålgruppe: teknisk kompetente brukere uten dyp prompt engineering-erfaring — utviklere, analytikere, fagledere — som bruker LLM-er i arbeidsflyt eller produktutvikling.

---

## 2. Mål og suksesskriterier

| Mål | Kriterium |
|-----|-----------|
| Reduser trial-and-error | Ferdig prompt er direkte brukbar uten manuell redigering |
| Model-bevissthet | Verktøyet gir ulik veiledning per modell |
| Structured output | JSON schema genereres automatisk og kan kopieres/eksporteres |
| Lav friksjon | Ferdig prompt generert på under 3 minutter |
| Ingen backend | Kjører 100 % i nettleseren, ingen server eller database |

---

## 3. Funksjonelle krav

### 3.1 Modellvelger

Bruker velger målmodell fra en liste. Valget styrer best practice-hints og output-format gjennom hele flyten.

Støttede modeller (v1):
- Claude Sonnet 4 / Opus 4 (Anthropic)
- GPT-4.1 / GPT-4o (OpenAI)
- Gemini 2.0 Flash / Pro (Google)
- Llama 3 (lokal / Ollama)
- Mistral / Mixtral (lokal / Ollama)
- Egendefinert (fritekst)

Hvert modellvalg laster inn en `ModelProfile`-konfigurasjon (se seksjon 7) som inneholder:
- Anbefalte strukturelementer
- Kjente quirks og advarsler
- Støttede output-formater
- Maks kontekstvindu (informativt)

### 3.2 Guided wizard — konstruksjonssteg

Wizarden er lineær men tillater å hoppe tilbake. Hvert steg viser en forklaring og et best practice-hint tilpasset valgt modell.

**Steg 1 — Rolle og persona**
- Fritekstfelt: "Hvem skal modellen være?"
- Eksempler og hint per modell
- Advarsel dersom feltet er tomt og modellen er rollesensitiv

**Steg 2 — Oppgave og kontekst**
- Hva skal gjøres (kjerneoppgave)
- Relevant bakgrunnsinformasjon
- Hint om chain-of-thought for reasoning-modeller

**Steg 3 — Input-spesifikasjon**
- Beskriv hva som sendes inn (fritekst, dokument, JSON, kode, etc.)
- Valgfritt: legg til eksempel-input (few-shot)

**Steg 4 — Output-krav**
- Format: fritekst / markdown / JSON / kode / tabell
- Lengde og presisjonsnivå
- Tone og målgruppe
- Hvis JSON valgt → aktiver JSON schema-builder (se 3.3)

**Steg 5 — Constraints og guardrails**
- Hva skal modellen IKKE gjøre
- Språk, sikkerhetshensyn, domene-begrensninger
- Hint om negative instructions vs. positive framing

**Steg 6 — Few-shot eksempler (valgfritt)**
- Legg til 1–3 eksempel-par (input → output)
- Automatisk formatert i riktig syntaks per modell

### 3.3 JSON Schema Builder

Aktiveres når output-format er satt til JSON. Lar bruker definere forventet output-struktur:

- Legg til felt med navn, type (string / number / boolean / array / object), og om det er required
- Nøstede objekter støttes ett nivå dypt (v1)
- Genererer ferdig JSON Schema (draft-07) og legger det inn i prompten som instruksjon
- Eksporteres separat som `.json`-fil

### 3.4 Live prompt-forhåndsvisning

Til høyre for wizarden (eller under på mobil) vises den genererte prompten løpende oppdatert:

- System prompt og user prompt vist atskilt med tydelige labels
- Syntaksutheving for XML-tags (Claude), markdown og JSON
- Kopier til utklippstavle per seksjon
- Tegn- og token-estimat (grov tiktoken-approximasjon i JS)

### 3.5 Model-spesifikk best practice

Hvert steg viser en kontekstsensitiv hint-boks basert på valgt modell. Eksempler:

- Claude: "Bruk XML-tags (`<context>`, `<instructions>`) for å strukturere lange prompts"
- GPT-4.1: "Instruksjonsadheranse er sterk — vær eksplisitt og presis, unngå vage formuleringer"
- Gemini: "System prompt støttes — legg rollebeskrivelsen der, ikke i user-meldingen"
- Llama/lokal: "Kortere prompts gir bedre ytelse på kvantiserte modeller — vær konsis"
- Reasoning-modeller (o1, Claude opus extended thinking): "Ikke be modellen tenke steg-for-steg — det gjør den automatisk. Fokuser på *hva* du vil ha, ikke *hvordan*"

### 3.6 Eksport og kopiering

- Kopier full prompt (system + user) som én blokk
- Kopier system prompt og user prompt separat
- Last ned som `.txt`
- Last ned JSON schema som `.json` (hvis brukt)
- Last ned hele konfigurasjonen som `.json` (kan lastes tilbake for videre redigering — v2)

---

## 4. Ikke-funksjonelle krav

| Krav | Beskrivelse |
|------|-------------|
| Enkeltfil | Hele applikasjonen i én `index.html` — ingen build-steg, ingen avhengigheter som krever npm |
| Ingen backend | Alt kjører i nettleseren. Ingen API-kall, ingen logging, ingen cookies |
| Responsiv | Fungerer på desktop og tablet. Mobil: sekundær prioritet |
| Tilgjengelighet | Tastaturnavigasjon gjennom wizard-steg. ARIA-labels på interaktive elementer |
| Ytelse | Lastetid < 1 sek (ingen tunge rammeverk — vanilla JS eller alpinejs via CDN) |
| Nettleserstøtte | Siste versjon av Chrome, Firefox, Safari, Edge |

---

## 5. UI/UX-prinsipper

- Wizard-flyt med tydelig progress-indikator (steg X av N)
- Hvert steg har: tittel, forklaring (1–2 setninger), input-felt, best practice-hint (kollapsbar)
- Primærknapp: "Neste steg" — sekundærknapp: "Tilbake"
- Fargepalett: mørkt tema som standard (VS Code-inspirert), lyst tema som alternativ via toggle
- Skrifttype: monospace for prompt-output, sans-serif for UI
- Ingen modal-dialoger — all interaksjon skjer inline

---

## 6. Teknisk arkitektur

```
index.html
├── <style>          CSS (custom properties, dark/light theme)
├── <body>
│   ├── #sidebar     Modellvelger + progress
│   ├── #wizard      Steg-for-steg wizard
│   └── #preview     Live prompt-forhåndsvisning
└── <script>
    ├── modelProfiles.js   (inlined) — modelldatabaser
    ├── wizard.js          (inlined) — steg-logikk og state
    ├── promptBuilder.js   (inlined) — konstruerer prompt fra state
    ├── schemaBuilder.js   (inlined) — JSON schema UI og generering
    └── export.js          (inlined) — kopiering og nedlasting
```

State-modell (vanilla JS objekt):
```js
{
  selectedModel: "claude-sonnet-4",
  currentStep: 1,
  fields: {
    role: "",
    task: "",
    context: "",
    inputSpec: "",
    fewShotExamples: [],
    outputFormat: "markdown",
    outputLength: "medium",
    tone: "",
    constraints: "",
    jsonSchema: null
  }
}
```

---

## 7. ModelProfile-konfigurasjon

Eksempel for Claude Sonnet 4:

```js
{
  id: "claude-sonnet-4",
  displayName: "Claude Sonnet 4",
  vendor: "Anthropic",
  contextWindow: 200000,
  supportsSystemPrompt: true,
  preferredStructure: "xml-tags",
  hints: {
    role: "Claude responderer godt på klart definerte roller. Bruk system prompt.",
    task: "XML-tags som <instructions> og <context> øker presisjon vesentlig.",
    reasoning: "Ikke instruer Claude om å tenke steg-for-steg — det skjer automatisk med extended thinking.",
    json: "Be eksplisitt om JSON i system prompt og inkluder schema som <output_schema>.",
    constraints: "Negative instruksjoner fungerer — 'ikke inkluder' er forstått.",
    fewShot: "Legg eksempler i <examples>-tags for best effekt."
  },
  outputFormats: ["markdown", "json", "plain", "code", "xml"],
  systemPromptTemplate: "<role>\n{{role}}\n</role>\n\n<instructions>\n{{task}}\n</instructions>",
  userPromptTemplate: "<context>\n{{context}}\n</context>\n\n{{inputSpec}}"
}
```

---

## 8. Prompt-konstruksjonslogikk

Prompten bygges i `promptBuilder.js` ved å:

1. Hente `ModelProfile` for valgt modell
2. Erstatte variabler i `systemPromptTemplate` og `userPromptTemplate`
3. Legge til few-shot-eksempler i riktig format per modell
4. Appende JSON schema-instruksjon hvis output er JSON
5. Appende constraints-seksjon
6. Returnere `{ systemPrompt: string, userPrompt: string }`

---

## 9. Avgrensninger — v1

Følgende er utenfor scope for første versjon:

- Brukerkontoer og lagring av prompts
- A/B-testing eller evaluering av output-kvalitet
- Direkte API-kall til LLM-er fra verktøyet
- Import av eksisterende prompts for analyse
- Agentiske prompts / multi-turn prompt-design
- Bilder og multimodal input-spesifikasjon

---

## 10. Mulige v2-funksjoner

- Last inn / lagre prompt-konfigurasjon som JSON
- Direkte test mot valgt modell via API-nøkkel (lokal lagring)
- Side-by-side sammenligning av samme prompt på to modeller
- Prompt-bibliotek med templates per brukstilfelle (klassifisering, ekstrahering, generering, etc.)
- Norsk/engelsk språkvalg i UI

---

## 11. Leveransekrav til Claude Code

- Enkelt fil: `prompt-builder.html`
- Ingen npm, ingen build-steg
- CDN-avhengigheter tillatt (kun én: f.eks. Alpine.js eller ingen)
- Alle ModelProfile-data hardkodet i filen
- Kommentert kode med tydelig seksjonsinndeling
- Kjørbar ved å åpne filen direkte i nettleser (file://-protokoll)

---

## 12. Testscenarioer (akseptansekriterier)

| Scenario | Forventet resultat |
|----------|--------------------|
| Velg Claude, fyll alle steg, velg JSON output | Ferdig system prompt med XML-tags + JSON schema-instruksjon |
| Velg Llama lokal, fyll kun rolle og oppgave | Kort, konsis prompt uten unødvendig struktur |
| Velg GPT-4.1, legg til 2 few-shot eksempler | Eksempler formatert korrekt i OpenAI chat-format |
| Klikk "Kopier system prompt" | Kun system prompt i utklippstavle |
| Last ned JSON schema | Gyldig JSON Schema draft-07 fil |
| Åpne i Safari uten internettilgang | Applikasjonen fungerer fullt ut |
