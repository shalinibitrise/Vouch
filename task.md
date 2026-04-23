
---

# 3. Build Tasks (Checklist)
*Ordered by dependency, highlighting the new "Cost" tasks.*

```markdown
# Vouch MVP v1.2 — Build Tasks

## Phase 1: Foundation
- [ ] Initialize Next.js 14 + Tailwind + shadcn/ui
- [ ] Setup Supabase Client & Environment Vars
- [ ] **Configure OpenRouter API Key**

## Phase 2: E1 — Account (PRD-001, 002, 003)
- [ ] Supabase Auth (Email/Pw + Google SSO)
- [ ] Workspace Creation & Onboarding
- [ ] Team Invites

## Phase 3: E2 — Jira (PRD-010, 011, 012, 013)
- [ ] Jira OAuth 2.0 Flow
- [ ] Ticket Ingestion (Store `content_hash`)
- [ ] Browsable Ticket List

## Phase 4: E3 — Figma (PRD-020, 021, 022, 022B, 023)
- [ ] Figma OAuth 2.0 Flow
- [ ] **NEW: Implement `figmaToSemanticModel` (Strip to minimal JSON)**
- [ ] Store `minimal_json` in DB
- [ ] Versioned Snapshots

## Phase 5: The Brain — LLM Wrapper (PRD-INF)
- [ ] **Create `lib/llm.ts`**
- [ ] Implement `callLLM` function
- [ ] Setup Routing: DeepSeek (Gen) / Gemini (Summary) / GPT (Fallback)
- [ ] Add JSON Validation & Retry Logic

## Phase 6: E4 — Generation (PRD-030, 031, 032, 033, 034)
- [ ] Link Jira to Figma Frames
- [ ] **Generate Test Cases via DeepSeek (using minimal_json)**
- [ ] Add 8k Token Guard
- [ ] Review/Approve UI
- [ ] Transcript Upload

## Phase 7: E5 — Drift (PRD-040, 041, 042, 043, 044)
- [ ] Jira Drift Detection (Hash check)
- [ ] **Summarize Drift via Gemini Flash**
- [ ] Figma Drift Detection (Compare `minimal_json`)
- [ ] Review Queue UI
- [ ] **Auto-Update (Delta Mode via DeepSeek)**

## Phase 8: E6, E7, E8 + Polish
- [ ] Stale Bug Detection
- [ ] Dashboard & Inventory
- [ ] In-App Notifications
- [ ] Email Digest (Resend)
- [ ] Deploy to Vercel