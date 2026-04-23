Here is the fully updated **PRD v1.2** in clean **Markdown** format.

I have removed the raw HTML tags and formatted it so you can easily copy-paste this into **Notion, GitHub, or a Markdown Editor**.

---

# Vouch — MVP v1.2 User Stories (Cost-Optimized)
**Owner:** Shalini | **Status:** Ready for build | **Timeline:** 8 weeks
**New Strategy:** OpenRouter (DeepSeek + Gemini Flash + GPT Fallback)

---

## 01 · Product Context & Strategy

### Why Vouch exists
Most QA teams live inside a silent contradiction: test cases written one sprint ago are already slightly wrong because the AC shifted, the Figma frame changed, or the user story was rewritten. Vouch sits between Jira, Figma, and your test suite, remembers the source-of-truth version each test case was authored against, and refuses to let the link go stale.

### The "Cost-Optimized" Strategy
We are cutting LLM costs (est. $0.50–$2.00 per run) by introducing a **"Smart Routing"** layer via **OpenRouter**.

1.  **DeepSeek Chat (Primary):** Used for `PRD-031` (Generation) and `PRD-044` (Auto-update). It is ~50x cheaper than Claude and excellent at structured JSON output.
2.  **Gemini Flash (Summaries):** Used for `PRD-040/041` (Drift Summaries). It has a massive context window (1M tokens) and is incredibly cheap for summarizing "what changed."
3.  **Semantic Proxy (PRD-022B):** We strip Figma JSONs of vectors/coordinates **before** sending them to the LLM, reducing payloads from 50k tokens to ~2k tokens.

#### In Scope for v1
- Jira Cloud + Figma integration
- **Test case generation via DeepSeek**
- **Drift detection summaries via Gemini Flash**
- Optional meeting transcript as context
- Version-snapshotted link between test case and source
- Basic dashboard + in-app notifications

#### Out of Scope for v1
- User story generation from transcripts
- Automation script generation (Playwright/Cypress) — v2
- Test execution + reporting — v3
- Bug auto-filing into Jira — v3

#### Primary Persona
**Maya, QA Lead at a 40-person product startup.** 8 years experience, manages 2 mid-level QAs. Lives in Jira and Figma daily. Gets burned twice a quarter by a production bug that slipped past a green suite because the test case no longer matched the feature.

---

## 02 · Epic Summary

| Code | Epic | Stories | Points | LLM Strategy |
| :--- | :--- | :--- | :--- | :--- |
| **E1** | Account & Workspace | 3 | 7 | *None* |
| **E2** | Jira Integration | 4 | 16 | *None* |
| **E3** | **Figma + Semantic Proxy** | **5** | **22** | **Strip JSON before LLM** |
| **E4** | **Test Case Generation** | **5** | **23** | **DeepSeek (Primary)** |
| **E5** | **Drift Detection** | **5** | **31** | **Gemini Flash (Summaries)** |
| **E6** | Stale Bug Detection | 3 | 12 | Gemini Flash |
| **E7** | Dashboard & Inventory | 2 | 8 | *None* |
| **E8** | Notifications | 2 | 6 | *None* |
| | **Total** | **29** | **125** | |

---

## 03 · User Stories

### EPIC 3: Figma Integration (Cost-Optimized)
*The goal here is to never send "noisy" data (coordinates, vectors) to the expensive LLM.*

#### PRD-022: Parse Figma frames into Minimal Semantic Data
*   **Narrative:** As Vouch's test generator, I need a **minimal** representation of each Figma frame—stripping out vectors and coordinates—so that the LLM input token count is reduced by ~80%.
*   **Acceptance Criteria:**
    *   Fetch document tree via `GET /v1/files/:file_key`.
    *   **STRIP:** Remove `x`, `y`, `width`, `height`, rotation, blend modes, and raw vector data.
    *   **KEEP:** Node ID, Name, Type (Button/Input), Text Content, Component Properties (Variants), and Image Refs.
    *   Store a `minimal_json`. Future LLM calls **must** use `minimal_json` ONLY.
*   **💰 Cost Impact:** Reduces payload from ~50k tokens to ~2k tokens.

#### NEW: PRD-022B: Figma Semantic Proxy Service
*   **Narrative:** As a Developer, I want a middleware service that converts raw JSON into a "TestCase-Ready" format, so we never accidentally send expensive, useless metadata to the LLM.
*   **Acceptance Criteria:**
    *   Create transformation function `figmaToSemanticModel(raw_json)`.
    *   Output schema: `{ frame_name, elements: [{type, text, role, state}] }`.
    *   Unit tests proving a 40k token input reduces to < 2k tokens.
    *   This service is called immediately after PRD-022 ingestion.

---

### EPIC 4: Test Case Generation (Optimized)
*The goal here is to strictly limit payload size and use "Delta Mode" for updates.*

#### PRD-031: Generate test cases (DeepSeek Powered)
*   **Narrative:** As a QA, I want Vouch to generate test cases using DeepSeek via OpenRouter, so my costs are low enough to offer a generous free tier.
*   **Acceptance Criteria:**
    *   **Input:** Semantic JSON (from PRD-022B) + AC Text.
    *   **Model:** Call `callLLM({ taskType: 'generate_test' })` which routes to `deepseek/deepseek-chat` via OpenRouter.
    *   **System Prompt:** "You are a test case generator. Output strict JSON matching the provided schema. Do not output markdown formatting or explanations."
    *   **Token Guard:** If payload (AC + Minimal JSON) exceeds **8,000 tokens**, reject generation and ask the user to select fewer frames.
    *   **Fallback:** If DeepSeek returns invalid JSON, retry once. If fails again, trigger GPT-4-turbo fallback.
    *   **Cost Target:** ~$0.10 per generation.

#### PRD-044: One-click Auto-update (Delta Mode)
*   **Narrative:** As a QA, I want to update test cases using only the "Diff" via DeepSeek, making updates nearly free ($0.01–$0.05).
*   **Acceptance Criteria:**
    *   **Input:** Current Test Case + JSON Patch (Diff).
    *   **Model:** `deepseek/deepseek-chat`.
    *   **Prompt:** "Here is the old test case. Here is the diff of the Figma frame. Update the test case *only* where the diff indicates a change. Preserve all other steps."
    *   **Cost:** Estimated at $0.01–$0.05 per update because the context window is tiny (just the diff + existing case).

---

### EPIC 5: Drift Detection (Optimized)
*The goal here is to use the cheapest model for text summarization.*

#### PRD-040: Detect AC change & Summarize (Gemini Flash)
*   **Narrative:** As Vouch's drift engine, I need to summarize Jira changes using Gemini Flash, so summarizing text is nearly free.
*   **Acceptance Criteria:**
    *   On hash mismatch, call `callLLM({ taskType: 'summarize_diff', ... })`.
    *   Routes to `google/gemini-flash-1.5` via OpenRouter.
    *   **Prompt:** "Summarize the material differences between these two texts in one sentence for a QA engineer."
    *   **Why Gemini:** It has a massive context window (handling large AC changes) and costs fractions of a penny.

---

## 04 · Build Timeline

| Weeks | Focus | Key Stories | Milestone |
| :--- | :--- | :--- | :--- |
| **1–2** | Foundation + Jira | PRD-001→013 | ✅ Users can sign up, connect Jira |
| **3–4** | Figma + **Semantic Proxy** | PRD-020→030, **022B** | ✅ Figma connected, JSON stripped |
| **5–6** | **LLM Wrapper** + Generation | PRD-031→034, **INF** | ✅ "Wow moment" — Cheap test generation via DeepSeek |
| **7–8** | Drift + **Gemini Summaries** | PRD-040→044 | ✅ Full loop — Cheap drift detection |

---

## 05 · Risks & Cost Controls

| Risk | Mitigation Strategy |
| :--- | :--- |
| **LLM Cost per ticket** | **Solved:** DeepSeek + Semantic Proxy + Gemini routing brings cost down to **~$0.10/gen** (vs $2.00 originally). |
| **DeepSeek JSON Reliability** | **Solved:** The `callLLM` wrapper includes a retry-once logic. If JSON parse fails, it switches to `GPT-4-turbo` for that specific request. |
| **Token Limit Failures** | **Solved:** Hard 8k token limit in PRD-031. System gracefully errors out ("Select fewer frames") rather than trying to process massive payloads. |
| **OpenRouter Rate Limits** | Keep the `callLLM` wrapper abstracted so you can swap back to direct API keys if OpenRouter fails critically. |

---

### How to use this document
Paste the **"Token Guard"** logic from PRD-031 and the **"Semantic Proxy"** logic from PRD-022B into your technical specification immediately. These are the highest leverage changes for protecting your budget.

**Next Step:** I will provide the `lib/llm.ts` (OpenRouter Wrapper) code and the `lib/figma/parser.ts` (Semantic Proxy) code to get you started.