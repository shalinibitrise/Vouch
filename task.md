# Vouch MVP v1 — Build Tasks

## Phase 1: Project Setup
- [/] Initialize Next.js 14 project with App Router
- [ ] Configure Tailwind CSS + shadcn/ui
- [ ] Set up Supabase client + environment variables
- [ ] Create design system (colors, typography, layout components)
- [ ] Set up project structure (folders, layouts, middleware)

## Phase 2: E1 — Account & Workspace (PRD-001, 002, 003)
- [ ] Auth pages (login, signup) with Supabase Auth
- [ ] Email verification flow
- [ ] Workspace creation (onboarding)
- [ ] Workspace settings page
- [ ] Team invites (magic link, roles)
- [ ] Workspace switcher dropdown

## Phase 3: E2 — Jira Integration (PRD-010, 011, 012, 013)
- [ ] Jira OAuth 2.0 (3LO) flow
- [ ] Project/issue type selection UI
- [ ] Jira ticket ingestion engine
- [ ] Browsable ticket list + drawer
- [ ] 10-minute cron re-sync

## Phase 4: E3 — Figma Integration (PRD-020, 021, 022, 023)
- [ ] Figma OAuth 2.0 flow
- [ ] Auto-discover Figma links from Jira tickets
- [ ] Figma frame parser (node tree → structured JSON)
- [ ] Versioned frame snapshots

## Phase 5: E4 — Test Case Generation (PRD-030, 031, 032, 033, 034)
- [ ] Link Jira story to Figma frames
- [ ] LLM test case generation
- [ ] Transcript upload
- [ ] Review/edit/approve UI
- [ ] Persist with source bindings

## Phase 6: E5 — Drift Detection (PRD-040, 041, 042, 043, 044)
- [ ] Jira drift detection
- [ ] Figma drift detection (material vs cosmetic)
- [ ] "Review Needed" flagging + diff view
- [ ] Review queue page
- [ ] One-click auto-update

## Phase 7: E6 — Stale Bug Detection (PRD-050, 051, 052)
- [ ] Parent story tracking
- [ ] Stale bug flagging
- [ ] Triage actions

## Phase 8: E7 — Dashboard & Inventory (PRD-070, 071)
- [ ] Workspace home dashboard
- [ ] Test case inventory with filters/search/export

## Phase 9: E8 — Notifications (PRD-080, 081)
- [ ] In-app notification center
- [ ] Daily email digest

## Phase 10: Polish & Deploy
- [ ] Deploy to Vercel
- [ ] Push to GitHub
- [ ] Performance testing
- [ ] Final walkthrough
