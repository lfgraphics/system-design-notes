# Jahannuma

## Context
Jahannuma is a multilingual digital library built with **Next.js** that organizes classical Urdu poetry and literature with English and Hindi alternates. The content is sourced from **Airtable** and exposed via structured, SEO-ready routes. The system focuses on fast delivery, localized experiences, and operational reliability.

**Constraints:**
- Airtable as the primary content store
- Need for multilingual content without duplicating data
- SEO and PWA requirements for low-bandwidth users
- Minimal backend infrastructure outside of Next.js and external integrations

---

## System Requirements

**Functional**
- Multilingual browsing of literature content
- SEO-friendly static or dynamic routes per language
- Likes/favorites for users stored via Clerk metadata
- Offline-friendly Progressive Web App behavior

**Non-Functional**
- Fast performance across locales
- Resilient to slow or failing upstream (Airtable)
- Consistent API response formats
- Secure handling of API keys and user metadata

---

## Architecture Overview

**Backend / API**
- Next.js App Router API endpoints for content lists and records
- Central Airtable client with schema validation and error handling
- Common response helpers for validation and success/error shaping

**Frontend**
- Language-specific directories: Urdu at root, English under `/EN`, Hindi under `/HI`
- Hooks for data fetching using **SWR** with caching and retry logic
- SEO generation components that vary per language
- PWA support and offline fallbacks

**Data Layer**
- Airtable used as the single source of truth
- Field mapping layer to avoid duplicating multilingual content fields
- Normalization utilities for Airtable records

**Integrations**
- **Clerk** for user identity and public metadata storage of likes
- Optional PDF viewing via viewer + proxy for range requests

---

## Key Decisions

### Airtable as Primary Store
- **Why:** Fast content management and minimal backend overhead  
- **Trade-offs:** Requires strict field validation to avoid schema drift  
- **Decision:** Keep Airtable, implement validation layer for safe frontend consumption

### Likes Management
- **Why:** Avoid dedicated database  
- **Choice:** Use Clerk public metadata  
- **Trade-offs:** Approximate counts under high concurrency; reconciled via revalidation

### Next.js App Router
- **Why:** Co-located UI and API logic; localized route support  
- **Trade-offs:** Increased surface area with language mirrors

---

## Patterns Used

- API validation + consistent response structure  
- Language-aware routing with parallel content mirrors  
- Universal data fetching (SWR) with caching and retry  
- Error fallbacks for unreachable upstream services

---

## Automation & Optimization

- Shared data hooks and utility modules for consistent data access
- Centralized field mapping to eliminate redundant localized fields
- Cache keys and TTL strategies to reduce network load
- Build-safe fallback content to maintain SEO builds under failure

---

## Risks & Limitations

- Airtable schema drift requires ongoing curation
- Likes count is eventually consistent, not strictly real-time under load
- Localization surface area increases maintenance overhead

---

## Retrospective

If rebuilt today:
- Add admin tools for schema validation and field mapping auditing
- Expand server-side rendering only where SEO impact is highest
- Use a more performant database
- Cache more frequently accessed data
- Implement Elasticsearch for searches