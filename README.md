# SupportIQ — AI-Powered Customer Support SaaS Platform

A production-grade multi-tenant AI helpdesk platform with RAG-based responses, ticket management, and a premium dark-canvas design system.

## Live URLs
- **Production**: https://supportiq.pages.dev (Cloudflare Pages)
- **Landing Page**: `/`
- **Dashboard**: `/dashboard`
- **AI Widget Demo**: `/widget`
- **Sign In**: `/login`
- **Sign Up**: `/signup`

## Features Implemented

### ✅ Core Platform
- **Multi-tenant Architecture**: Workspace-based isolation with subdomain routing concept
- **Landing Page**: Full marketing site with hero, features grid, pricing, CTA sections
- **Auth System**: Login/Signup with Google OAuth mockup and form auth
- **Dark Canvas Design System**: Framer-inspired with Violet/Magenta gradient accents

### ✅ AI Knowledge Base
- Upload area with drag-and-drop (PDF, DOCX, TXT, MD)
- Simulated chunking pipeline (~500 tokens) with progress visualization
- Vector embedding status (Qdrant integration ready)
- Multiple knowledge bases per tenant
- Re-indexing workflow UI

### ✅ AI Chat Widget (Embeddable)
- Floating circular chat button (bottom-right)
- RAG-based responses via `/api/chat` endpoint
- AI typing indicators
- Source citation pills from knowledge base
- Auto-escalation logic (confidence threshold + keyword triggers)
- "Talk to agent" human handoff
- Suggested questions on first load

### ✅ Ticket Management
- Full ticket list with status, priority, assignee columns
- Status badges: Open, In Progress, Resolved, Closed
- Priority levels: Low, Medium, High, Urgent
- Round-robin assignment UI

### ✅ Agent Inbox
- Unified conversation list (AI Chats + Escalated)
- Real-time chat panel with AI→Human handoff notice
- Canned responses/macros toolbar
- SLA countdown display
- Customer context panel

### ✅ Analytics Dashboard
- AI Resolution Rate, CSAT Score, Avg Response Time metrics
- Ticket volume trend chart (Chart.js)
- Resolution breakdown donut chart
- Knowledge Gap Analysis (queries AI failed)
- Agent performance metrics

### ✅ Settings
- Workspace configuration
- AI confidence threshold slider
- Escalation keyword configuration
- Widget embed code generator with copy button
- Widget preview link

## Design System

### Color Tokens
| Token | Value | Usage |
|-------|-------|-------|
| canvas | #090909 | Base background |
| surface-1 | #141414 | Cards |
| surface-2 | #1c1c1c | Elevated cards |
| hairline | #262626 | Borders |
| ink | #ffffff | Primary text |
| ink-muted | #999999 | Secondary text |
| accent-blue | #0099ff | Links, focus rings only |
| gradient-violet | #6a4cf5 | Primary gradient |
| gradient-magenta | #d44df0 | Secondary gradient |
| gradient-orange | #ff7a3d | Tertiary gradient |
| semantic-success | #22c55e | Success states |

### Typography
- Display headings: GT Walsheim (system fallback) — aggressive negative letter-spacing
- Body/UI: Inter Variable throughout
- display-xxl: 110px / -5.5px tracking (hero)
- display-lg: 62px / -3.1px tracking (section headers)
- display-md: 32px / -1.0px tracking

### Components
- `btn-primary`: White pill (100px radius)
- `btn-secondary`: Charcoal pill (#1c1c1c)
- `gradient-violet-card`: Violet→Magenta gradient (30px radius)
- `gradient-magenta-card`: Magenta→Coral gradient
- `card`: surface-1 with hairline-soft border
- `card-elevated`: surface-2 with hairline border

## API Endpoints

| Method | Path | Description |
|--------|------|-------------|
| GET | `/api/health` | Health check |
| GET | `/api/stats` | Dashboard statistics |
| GET | `/api/tickets` | Ticket list |
| GET | `/api/knowledge-bases` | KB list |
| POST | `/api/chat` | AI chat endpoint (mock RAG) |

## Tech Stack
- **Backend**: Hono.js on Cloudflare Workers
- **Frontend**: Vanilla HTML/CSS/JS with Tailwind CSS (CDN)
- **Charts**: Chart.js (CDN)
- **Icons**: Font Awesome (CDN)
- **Build**: Vite + @hono/vite-cloudflare-pages
- **Deploy**: Cloudflare Pages

## Database Schema (Ready for Production)

```sql
-- tenants
CREATE TABLE tenants (id, name, subdomain, plan, created_at);

-- users  
CREATE TABLE users (id, tenant_id, role, email, name, created_at);

-- knowledge_bases
CREATE TABLE knowledge_bases (id, tenant_id, name, status, created_at);

-- documents
CREATE TABLE documents (id, kb_id, filename, chunk_count, indexed_at);

-- conversations
CREATE TABLE conversations (id, tenant_id, customer_identifier, status, channel);

-- messages
CREATE TABLE messages (id, conversation_id, sender_type, content, created_at);

-- tickets
CREATE TABLE tickets (id, conversation_id, assigned_agent_id, priority, status, sla_deadline);
```

## RAG Pipeline (Architecture)

1. **Document Upload** → Chunk into ~500 token segments → OpenAI `text-embedding-3-small` → Store in Qdrant with `{tenant_id, doc_id, page}` metadata
2. **Query Processing** → Embed query → Qdrant semantic search (top 5, filtered by `tenant_id`) → GPT-4o with strict system prompt
3. **Confidence Scoring** → Log each response with relevance score → Auto-escalate if score < threshold

## Deployment

### Platform: Cloudflare Pages
- **Build command**: `npm run build`
- **Output directory**: `dist`
- **Node compatibility**: `nodejs_compat` flag enabled

### Status
- ✅ Production deployed on Cloudflare Pages
- ✅ GitHub repository with full source code

## Features Not Yet Implemented (Future Roadmap)
- [ ] Real OpenAI GPT-4o + Qdrant RAG integration
- [ ] Stripe billing integration
- [ ] Real-time WebSocket via Pusher
- [ ] Email notifications (SendGrid/Resend)
- [ ] SSO/SAML for Enterprise
- [ ] Custom subdomain routing per tenant
- [ ] Cloudflare D1 for persistent data storage
- [ ] R2 for document file storage
- [ ] Webhook system (Slack, email)
- [ ] Mobile app (React Native)

## Development

```bash
npm run build         # Build for production
npm run dev:sandbox   # Run locally with wrangler
pm2 start ecosystem.config.cjs  # Start with PM2
```

---
Built with ❤️ using Hono + Cloudflare Workers + Tailwind CSS
