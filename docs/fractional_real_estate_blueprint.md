# Fractional Real Estate Investment Platform Blueprint

## 1. Product Vision (One-Page)
### 1.1 Problem Statement
- Local property owners struggle to unlock capital quickly without selling 100% ownership or navigating institutional processes; retail investors lack fractional, transparent access to nearby real-estate opportunities.
- Existing crowdfunding platforms focus on large commercial deals, lack hyper-local discovery, and have opaque compliance flows.

### 1.2 Target Segments
1. **Property Owners**: Individuals or small businesses with residential or small commercial assets seeking capital or partial exit.
2. **Retail Investors**: Location-conscious, moderate net-worth individuals seeking diversified, inflation-hedged investments.
3. **Admins & Compliance Officers**: Internal teams ensuring regulatory adherence, fraud prevention, and operational oversight.

### 1.3 Value Proposition
- **For Owners**: Fast, compliant access to fractional capital with escrow and verification handled end-to-end.
- **For Investors**: Curated, nearby deals with transparent due diligence, pro-rata ownership, and digital distribution of returns.
- **For Platform**: Scalable marketplace with compliance-first operations, auditability, and future secondary liquidity.

### 1.4 Key Jobs-To-Be-Done
- Owners list, verify, and launch funding campaigns seamlessly.
- Investors discover local properties, evaluate risk/reward, commit funds, and track performance.
- System automates escrow, ownership issuance, and distributions while maintaining compliance logs.

### 1.5 Success Metrics
- **Acquisition**: % of verified owners who launch within 14 days; investor activation rate (first commitment within 7 days).
- **Liquidity**: Median time-to-fill campaigns; average funding velocity per property.
- **Compliance**: 100% KYC completion, <0.5% flagged transactions without resolution within SLA.
- **Retention**: 60-day investor repeat commitment rate; reinvestment of distribution proceeds >35%.
- **Monetization**: Net revenue per campaign; contribution margin per investor cohort.

## 2. Product Requirements Document (PRD)
### 2.1 Personas
- **Owner Olivia**: Age 45 landlord wanting to raise $200k for renovations; moderate tech proficiency.
- **Investor Isaac**: Age 32 professional seeking diversified local investments; expects mobile-friendly UI.
- **Admin Alex**: Platform operator monitoring listings, disputes, and trust signals.
- **Compliance Casey**: Oversees KYC/AML reviews, sanctions screening, suspicious activity reporting (SAR).

### 2.2 Core User Stories with Acceptance Criteria & Edge Cases
1. **Owner Listing & Campaign Launch**
   - *Story*: As an Owner, I can list a property, verify ownership, set funding target and minimum ticket, then launch after KYC and escrow setup.
   - *Acceptance Criteria*:
     - Owner completes identity and property ownership verification (title upload, utility bill).
     - Form validations: address (geocoded), funding target ≥ minimum threshold, min ticket ≤ target.
     - Escrow account linked via payment provider; API returns confirmation.
     - Listing state transitions: Draft → Verification Pending → Ready → Live.
   - *Edge Cases*: Partial verification (missing doc); title mismatch; sanctions hits; geo-restricted property; payment setup failure; owner attempts to change target post-launch (blocked).

2. **Investor Discovery & Commitment**
   - *Story*: As an Investor, I can discover nearby listings, view due diligence documents, commit funds into escrow, track progress, and receive ownership.
   - *Acceptance Criteria*:
     - Location services prompt; fallback manual search.
     - Listings filtered by radius, funding status, risk score; accessible alternative text & keyboard navigation.
     - Commitment validation: amount ≥ min ticket, ≤ remaining cap, payment instrument verified.
     - Escrow debit succeeded; commitment receipt emailed.
     - Campaign success triggers OwnershipLot issuance proportionally.
   - *Edge Cases*: Over-subscription (auto-proration or waitlist); expired payment method; sanctions flag post-commitment; investor from restricted jurisdiction; currency conversion rounding.

3. **System Campaign Success Settlement**
   - *Story*: As the System, when a campaign hits 100% and passes checks, I allocate OwnershipLots, settle funds, issue documents, and notify parties.
   - *Acceptance Criteria*:
     - Automatic status change Active → Pending Settlement when target reached.
     - Title/legal checks pass; compliance sign-off recorded.
     - OwnershipLots minted pro-rata, rounding to nearest 0.0001 shares; residual amounts handled via cash adjustment or platform reserve.
     - Settlement instructions executed; owner receives net proceeds; investors notified.
   - *Edge Cases*: Late fraud alert; payment capture failure; partial settlement due to bank holiday; investor withdrawal request before settlement; doc signing failure.

4. **System Campaign Failure & Refunds**
   - *Story*: As the System, if a campaign fails (deadline or cancel), I trigger automatic refunds net of fees and update states idempotently.
   - *Acceptance Criteria*:
     - Campaign status Active → Failed; commitments reversed; investors notified with timeline.
     - Refund engine handles partial captures; ensures escrow release; logs audit entries.
     - Idempotency key ensures multiple triggers safe.
   - *Edge Cases*: Payment already settled; expired payment method; network failure mid-refund; dispute raised; negative balances from promo credits.

5. **Investor Distribution Receipt**
   - *Story*: As an Investor, upon exit or income, I receive distributions pro-rata with statements and tax summaries.
   - *Acceptance Criteria*:
     - DistributionEvent created with gross amount, platform fee, taxes.
     - Payouts generated for each OwnershipLot holder; statements downloadable.
     - Notifications via email/push; ledger entries reconciled.
   - *Edge Cases*: Investor bank account closed; withheld tax requirements; dispute over allocation; negative cash flows.

### 2.3 Feature List
- **MVP**
  - Owner onboarding & listing creation with KYC.
  - Property verification workflow & document management.
  - Investor discovery, filtering, map view, campaign details.
  - Commitment flow with escrow payments, soft-cap/hard-cap logic.
  - Campaign lifecycle automation, ownership issuance, digital agreements (PPM, subscription docs).
  - Distribution module (manual trigger with automated allocation), statements, tax documents (summary 1099-like).
  - Admin console for approvals, audit logs, dispute management.
  - Notifications (email, in-app), multi-language (English + Spanish), WCAG 2.2 AA accessible components.

- **V2+ Roadmap**
  - Secondary trading bulletin board with compliance gating.
  - Mobile native wrappers, biometric login.
  - Advanced analytics dashboards for investors (IRR, yield projections).
  - AI-assisted property scoring, dynamic risk ratings.
  - Integration with local government records APIs for automated title checks.
  - Tax-advantaged accounts support (SDIRA, 1031 exchange guidance).
  - Social features (watchlists, co-investor groups).
  - Open API for partners (brokers, community banks).

### 2.4 Non-Functional Requirements
- **Performance**: P95 page load <2.5s on 4G; API response <300ms P95 for reads, <600ms for writes; support 100k DAU, 10k concurrent sessions.
- **Security**: OWASP ASVS Level 2 compliance; data encryption at rest (AES-256) & transit (TLS 1.3); RBAC with least privilege; audit logs immutable.
- **Privacy**: GDPR/CCPA-aligned consent, data minimization, right-to-erasure workflow within 30 days.
- **Availability**: 99.5% uptime MVP, 99.9% post-scale; multi-AZ deployment; RPO <1h, RTO <4h.
- **Observability**: Structured logging (JSON), distributed tracing, metrics (APM), real-time alerts.

### 2.5 Risk Register
| Risk Category | Description | Likelihood | Impact | Mitigation |
| --- | --- | --- | --- | --- |
| Market | Low owner adoption | Medium | High | Partnerships with local brokers, referral incentives, education content. |
| Regulatory | Real-estate securities classification ambiguity | High | High | Engage securities counsel, design SPV/series LLC structure, geofence restricted states, disclose not legal advice. |
| Operational | Fraudulent property listings | Medium | High | Manual review, third-party title verification, deposit requirement. |
| Technical | Payment provider downtime | Low | Medium | Multi-provider abstraction, retry logic, status dashboards. |
| Compliance | KYC false negatives | Medium | High | Multiple data vendors, manual escalation, continuous monitoring. |
| Data Privacy | Breach of PII | Low | High | Encryption, DLP tools, access reviews, incident response plan. |

## 3. UX & Information Architecture
### 3.1 Site Map & IA
- **Public**: Home → Explore Properties (Map/List) → Property Detail → Educational Resources → FAQs → Legal/Disclosures.
- **Owner Portal**: Dashboard → Create Listing → Verification → Campaign Management → Documents → Payouts.
- **Investor Portal**: Dashboard → Nearby Opportunities → Watchlist → Commitments → Portfolio → Distributions.
- **Admin Console**: Review Queue → Campaigns → Users → Compliance → Audit Logs → Reports.

### 3.2 Key User Flows
1. **Owner List-and-Verify**: Owner signs up → completes KYC → adds property details (address, photos, financials) → uploads verification docs → selects SPV structure → signs listing agreement → compliance review → escrow account link → campaign scheduled.
2. **Investor Discover-Evaluate-Invest**: Investor signs up → KYC/AML → location permission → browse map/list → filter (funding status, yield) → view property detail (docs, risk score) → commit funds (payment auth, escrow hold) → receive confirmation → track progress.
3. **Escrow Settlement**: Campaign reaches target → system locks commitments → compliance final check → payment capture → funds moved to escrow master → SPV distribution to owner minus fees → update ledgers.
4. **Distribution Flow**: Income event recorded → upload supporting docs → system calculates pro-rata amounts → compliance approval → payouts initiated → notifications + statements.

### 3.3 Low-Fidelity Wireframe Descriptions (Top 10 Screens)
1. **Landing Page**: Hero banner with CTA, map preview, trust badges; sections for how it works, testimonials, FAQs; footer with legal links. States: loading (skeleton cards), error (retry banner), empty (no featured properties).
2. **Explore Map/List**: Split map (left) + list (right) with filters (radius slider, property type, funding status). Cards show thumbnail, funding progress bar, yield. States: loading spinners; empty state with “expand radius”; error toast.
3. **Property Detail**: Carousel, funding progress, key metrics, tabs (Overview, Financials, Documents, Updates). Commitment widget with slider, min ticket reminder. States: pending verification banner; sold out badge; doc download error; tooltip for risk disclosures.
4. **Owner Dashboard**: Summary cards (active campaigns, funds raised), alerts (missing docs), table of listings with statuses. Empty state prompts to “Create listing”.
5. **Create Listing Wizard**: Multi-step form (Property basics, Financials, Documents, Funding terms, Review). Validation messages inline, save draft button. Error state for invalid uploads; accessibility for keyboard navigation.
6. **Verification Center**: Document upload list, status chips (Pending, Approved, Rejected), timeline of compliance events, contact support CTA. Error state for failed API (retry).
7. **Investor Commitment Modal**: Form with amount input, payment method selector, terms checkbox, summary (fees, expected ownership). States: loading while creating payment intent; error for insufficient funds; success confirmation with share estimate.
8. **Portfolio Dashboard**: Pie chart (accessible alt text), table of holdings, upcoming distributions, download statements button. Empty state encouraging first investment; error retrieving data.
9. **Distribution Detail Screen**: Breakdown of gross income, platform fee, taxes, net payout; timeline of payout status; document links (1099, statements). States: pending approval; failed payout banner with action to update bank.
10. **Admin Review Console**: Queue of pending actions with filters (KYC, property, campaign). Detail panel shows user info, documents, decision buttons, audit trail. Error state: API failure with fallback manual entry.

### 3.4 Content Strategy
- **Tone**: Trustworthy, transparent, educational; plain English explanations with compliance disclaimers.
- **Disclosures**: Highlight “Not legal or financial advice”; risk warnings on every commitment page; property-specific disclaimers (market, rental risk).
- **Educational Tooltips**: Explain SPV structure, minimum ticket, expected timelines; accessibility (ARIA labels). Multilingual copy (English/Spanish) with localized currency/date formats.

## 4. Data & Domain Modeling
### 4.1 Entity-Relationship Model (Simplified)
- **User** (`user_id` PK, `email` unique index, `role` enum [owner, investor, admin, compliance], `status`, `created_at`, `last_login`).
- **IdentityKYC** (`kyc_id` PK, `user_id` FK, `provider_session_id`, `status` enum [init, pending, approved, rejected], `risk_score`, `updated_at`). Index on (`user_id`, `status`).
- **Wallet** (`wallet_id` PK, `user_id` FK, `currency`, `available_balance`, `escrow_balance`, `status`). Unique per (`user_id`, `currency`).
- **PaymentMethod** (`pm_id` PK, `user_id` FK, `provider_pm_id`, `type`, `status`, `exp_date`, `last4`). Index on `status`.
- **Property** (`property_id` PK, `owner_user_id` FK, `address`, `city`, `state`, `country`, `geo_point` PostGIS, `property_type`, `valuation`, `sq_ft`, `created_at`, `verification_status`). Index on `geo_point` (GIST), `verification_status`.
- **Listing** (`listing_id` PK, `property_id` FK, `title`, `description`, `min_ticket`, `target_amount`, `hard_cap`, `currency`, `status`, `go_live_at`, `created_at`). Unique constraint `status != 'Live'` when `property_id` already live.
- **Verification** (`verification_id` PK, `property_id` FK, `type` enum [title, ownership, appraisal], `result`, `requested_at`, `completed_at`, `provider_ref`).
- **Campaign** (`campaign_id` PK, `listing_id` FK, `soft_cap`, `hard_cap`, `start_at`, `end_at`, `status` enum [draft, pre_launch, active, pending_settlement, successful, failed, refunded], `funded_amount`, `escrow_account_id`). Index on `status`, `end_at`.
- **Commitment** (`commitment_id` PK, `campaign_id` FK, `investor_user_id` FK, `amount`, `fees`, `status` enum [initiated, held, captured, refunded, failed], `payment_intent_id`, `created_at`). Unique constraint on (`payment_intent_id`).
- **EscrowTransaction** (`escrow_tx_id` PK, `type` enum [hold, capture, release, refund], `amount`, `currency`, `status`, `provider_tx_id`, `occurred_at`, `commitment_id` nullable, `campaign_id`). Index on `campaign_id`, `status`.
- **OwnershipLot** (`ownership_lot_id` PK, `campaign_id` FK, `investor_user_id` FK, `units`, `percentage`, `issued_at`, `status` enum [issued, transferred, cancelled]). Unique index on (`campaign_id`, `investor_user_id`).
- **DistributionEvent** (`distribution_id` PK, `campaign_id` FK, `type` enum [rental_income, sale_exit], `gross_amount`, `platform_fee_amount`, `tax_withheld`, `status` enum [draft, scheduled, processing, completed, failed], `scheduled_at`).
- **Payout** (`payout_id` PK, `distribution_id` FK, `ownership_lot_id` FK, `amount`, `status` enum [scheduled, processing, completed, failed], `provider_payout_id`, `initiated_at`). Index on `status`.
- **Document** (`document_id` PK, `entity_type` enum, `entity_id`, `type`, `storage_url`, `status`, `signed_by`, `created_at`). Composite index (`entity_type`, `entity_id`).
- **Message** (`message_id` PK, `channel` enum, `sender_user_id`, `recipient_user_id`, `subject`, `body`, `metadata`, `created_at`).
- **AuditLog** (`audit_id` PK, `actor_user_id`, `entity_type`, `entity_id`, `action`, `before`, `after`, `created_at`). Immutable (append-only).
- **Notification** (`notification_id` PK, `user_id` FK, `type`, `channel`, `status`, `payload`, `sent_at`). Index `user_id+status`.

#### Example Record (Campaign)
```
{
  "campaign_id": "cmp_123",
  "listing_id": "lst_789",
  "soft_cap": 150000.00,
  "hard_cap": 200000.00,
  "start_at": "2024-07-01T00:00:00Z",
  "end_at": "2024-09-01T00:00:00Z",
  "status": "active",
  "funded_amount": 95000.00,
  "escrow_account_id": "esc_456"
}
```

### 4.2 State Machines
- **Listing Lifecycle**: Draft → Verification Pending → Verification Failed (retry) / Verified → Ready (scheduled) → Live → Closed (success/failure) → Archived. Transitions require audit logs; manual override by Admin with reason.
- **Campaign Lifecycle**: Draft → Pre-Launch (marketing, commitments disabled) → Active (commitments accepted) → Pending Settlement (target reached) → Successful (settled) → Refunded (if post-settlement reversal) or Failed (deadline). Failed → Refunded after refunds processed.
- **KYC Lifecycle**: Init → Pending Review (provider) → Approved (expiry tracked) / Rejected (provide reason) → Resubmitted (loops to Pending). Suspended state when sanctions hit requiring manual review.
- **Payout Lifecycle**: Scheduled → Processing (payment provider) → Completed → Failed (retry) → Cancelled (manual). Failed transitions log error, trigger notifications.

### 4.3 Calculation Formulas
- **Pro-Rata Ownership**: `ownership_percentage = commitment_amount / total_success_amount`. Units represented as `ownership_units = (commitment_amount / property_equity_unit_value)`.
- **Fees**: Platform origination fee = `target_amount * origination_rate`; carry fee on exit = `(net_profit * carry_rate)`; listing fee fixed per property; payment fees per commitment.
- **IRR/XIRR**: Use cash flow series (negative initial commitments, positive distributions). `XIRR = IRR(cash_flows, dates)` implemented via Newton-Raphson iteration until convergence <1e-6.
- **Distribution Split**: For distribution `D`, `platform_fee = D * platform_fee_rate`, `tax_withheld = sum(investor_tax_withholding)`, `net_distributable = D - platform_fee - tax_withheld`. Investor share `= net_distributable * ownership_percentage`. Waterfall ensures owner catch-up if relevant.
- **Exit Waterfall**: On sale `gross_exit`, deduct closing costs + debt repayment → resulting equity `E`. Then `platform_carry = max(0, (E - preferred_return_threshold) * carry_rate)` → remainder distributed pro-rata.

## 5. Technical Architecture
### 5.1 Stack Choices & Justification
- **Frontend**: Next.js (App Router) + React + TypeScript + Tailwind CSS. Benefits: SSR for SEO, server components for performance, TypeScript safety, Tailwind for consistent styling, accessible component libraries (Headless UI). Form handling via React Hook Form + Zod validation.
- **Backend**: Node.js with NestJS (TypeScript). Provides modular architecture, dependency injection, strong typing, integrates with Prisma ORM, easy integration with event queues.
- **Persistence**: PostgreSQL with Prisma. PostGIS extension for geo queries; transactional integrity for financial records. Redis for caching (geocache, sessions) and queues (BullMQ).
- **Object Storage**: AWS S3 for documents with KMS encryption.
- **Auth**: Auth0 (OIDC) with passwordless email magic link + social login; short-lived access tokens (15 min) + refresh tokens; RBAC policies stored in Auth0/DB; session cookie for web with SameSite=Lax.
- **Payments/Escrow**: Stripe Treasury / Stripe Capital (if US) or Mangopay (EU). Supports escrow accounts, destination charges, split payouts. Use Connect Custom accounts for owners; investors pay via PaymentIntents.
- **KYC/AML**: Persona for identity + document verification. Webhooks for status updates; fallback manual review queue.
- **Maps/Geo**: Mapbox for map visualization; PostGIS for radius search.
- **Search**: Postgres trigram for MVP; upgrade to OpenSearch for fuzzy and scaling when >1M listings.
- **Notifications**: SendGrid for email, Firebase Cloud Messaging (FCM) for push; Twilio SMS for critical alerts (2FA optional).
- **E-signature**: Dropbox Sign (HelloSign) for subscription agreements.

### 5.2 Sequence Diagrams (Textual UML)
**a) Investor Commit → Settlement → Ownership**
```
Investor -> Frontend: Submit commitment form
Frontend -> Backend API: POST /campaigns/{id}/commitments (idempotency key)
Backend -> Auth0: Validate token
Backend -> KYC Service: Ensure investor approved
Backend -> Payments Provider: Create payment intent (capture later)
Payments Provider -> Backend: Intent=requires_capture
Backend -> DB: Save commitment (status=held)
Backend -> Frontend: Confirmation
Campaign Scheduler -> Backend: Check funding progress
Backend -> Payments Provider: Capture intents when 100%
Payments Provider -> Backend: Capture succeeded
Backend -> DB: Update commitments status=captured
Backend -> Ownership Service: Allocate OwnershipLots
Ownership Service -> DB: Insert OwnershipLots
Backend -> Notifications: Send success emails
```

**b) Campaign Failure → Refund**
```
Scheduler -> Backend: Campaign deadline reached
Backend -> DB: Update campaign status=failed
Backend -> Payments Provider: Cancel/capture reversal using idempotent keys
Payments Provider -> Backend: Refund success events
Backend -> DB: Update commitments status=refunded
Backend -> Notifications: Inform investors + owner
Audit Logger -> DB: Append failure + refund logs
```

**c) Owner Listing → Verification → Go-Live**
```
Owner -> Frontend: Complete listing wizard
Frontend -> Backend: POST /listings (status=draft)
Backend -> DB: Store listing, trigger verification workflow
Backend -> KYC Provider: Start owner KYC
KYC Provider -> Webhook: status=approved
Backend -> Verification Service: Request title check
Title Provider -> Backend: result=pass
Compliance Officer -> Admin UI: Approve listing
Backend -> Scheduler: Set go_live_at
Scheduler -> Backend: Activate campaign (status=active)
Backend -> Notifications: Notify subscribers
```

### 5.3 API Design Guidelines
- REST-first with `/v1` prefix; GraphQL for investor portfolio analytics (aggregation-heavy). JSON responses.
- Use idempotency keys on POST financial endpoints. Rate limits: 100 req/min per user for standard APIs, 20/min for financial operations. Pagination: cursor-based (`?cursor=`) with `limit` (max 100).
- Error format: RFC 9457-compatible problem+json (`type`, `title`, `status`, `detail`, `instance`, `errors[]`).

## 6. API Specifications (Selected)
### 6.1 Properties & Listings
- `POST /v1/properties`
  - Request: `{ "address": string, "geo": {"lat": number, "lng": number}, "type": "residential"|"commercial", "valuation": number, "documents": [doc_id] }`
  - Validations: address required, geo valid lat/lng, valuation >0.
  - Response `201`: `{ "property_id": "prop_123", "verification_status": "pending" }`
  - Errors: 400 invalid data, 409 duplicate property.

- `POST /v1/listings`
  - Request: `{ "property_id": "prop_123", "title": string, "description": string, "min_ticket": number, "target_amount": number, "hard_cap": number, "go_live_at": ISODate }`
  - Validations: `min_ticket >= 100`, `target_amount >= min_ticket*5`, `hard_cap >= target_amount`.
  - Response: `201 { "listing_id": "lst_456", "status": "verification_pending" }`

- `POST /v1/listings/{id}/verify`
  - Body: `{ "verification_type": "title"|"ownership", "document_ids": ["doc_1"], "notes": string }`
  - Response: `202` accepted; triggers verification workflow.

- `POST /v1/listings/{id}/publish`
  - Preconditions: Listing status `ready`; compliance approval recorded.
  - Response: `200 { "campaign_id": "cmp_123", "status": "active" }`

### 6.2 Campaigns
- `POST /v1/campaigns`
  - Request: `{ "listing_id": "lst_456", "soft_cap": number, "hard_cap": number, "start_at": ISODate, "end_at": ISODate }`
  - Validations: `end_at > start_at`, `soft_cap >= target_amount*0.5`.
  - Response: `201 {...}`.

- `POST /v1/campaigns/{id}/open`
  - Requires admin auth; sets status `active`. Response `200`.

- `POST /v1/campaigns/{id}/close`
  - Body: `{ "reason": "manual"|"completed" }`; closes campaign. Handles edge cases (commitments in flight). Response `200`.

- `POST /v1/campaigns/{id}/settle`
  - Idempotent; triggers settlement. Response includes summary.

### 6.3 Commitments
- `POST /v1/campaigns/{id}/commitments`
  - Headers: `Idempotency-Key`.
  - Request: `{ "amount": number, "payment_method_id": "pm_123", "auto_reinvest": boolean }`
  - Validations: amount >= min ticket, <= remaining hard cap, payment method active.
  - Response `201`: `{ "commitment_id": "cmt_456", "status": "held", "estimated_units": 12.3456 }`

- `POST /v1/commitments/{id}/cancel`
  - Preconditions: status in [initiated, held]; handles partial refunds.

- `POST /v1/commitments/{id}/capture`
  - For manual overrides; normal flow auto-captures.

### 6.4 KYC Sessions & Webhooks
- `POST /v1/kyc/sessions`
  - Request: `{ "user_id": "usr_123", "purpose": "invest"|"list" }`
  - Response: `{ "session_token": "persona_session" }`.
- `POST /v1/webhooks/kyc`
  - Receives `{ "session_id": "...", "status": "approved", "reason": null }`; validates HMAC.
  - Responds `200` on success; error `400` logs for retries.

### 6.5 Payments/Escrow
- `POST /v1/payments/intents`
  - Request: `{ "commitment_id": "cmt_456" }`
  - Response includes provider secret.

- `POST /v1/escrow/transactions`
  - Create manual escrow movements. Validation ensures `type` allowed.

- `POST /v1/payments/refunds`
  - Request: `{ "commitment_id": "cmt_456", "amount": number }`
  - Idempotent key required.

- `POST /v1/payouts`
  - Request: `{ "distribution_id": "dist_123" }`; triggers provider payout.

### 6.6 OwnershipLots
- `POST /v1/campaigns/{id}/ownership`
  - Body: `{ "investor_id": "usr_456", "units": number, "percentage": number }` for manual adjustments.
  - Future `POST /v1/ownership/{id}/transfer` with compliance checks.
  - `DELETE /v1/ownership/{id}` to burn on exit.

### 6.7 Distributions
- `POST /v1/campaigns/{id}/distributions`
  - Request: `{ "type": "rental_income", "gross_amount": number, "distribution_date": ISODate, "documents": [doc_id] }`
  - Response: `201 { "distribution_id": "dist_789", "status": "draft" }`.

- `POST /v1/distributions/{id}/calculate`
  - Body: `{ "auto_withhold_tax": boolean }`; response returns allocation preview.

- `POST /v1/distributions/{id}/execute`
  - Triggers payouts; handles partial failures with retry tokens.

### 6.8 Documents
- `POST /v1/documents/upload`
  - Request: `{ "entity_type": "property", "entity_id": "prop_123", "filename": string, "content_type": string }`
  - Response: pre-signed URL; validations on file type/size.

- `POST /v1/documents/{id}/sign`
  - Initiates e-sign workflow; response includes signing URL.

### 6.9 Notifications
- `POST /v1/notifications/preferences`
  - Request: `{ "channels": { "email": true, "push": false, "sms": false } }`.

- `POST /v1/notifications/subscribe`
  - Subscribe to property updates; validate duplicates.

### 6.10 Audit Logs
- `GET /v1/audit-logs`
  - Query params: `entity_type`, `entity_id`, `cursor`, `limit`.
  - Response: list with pagination metadata; only accessible to admin/compliance.

## 7. Compliance, Risk, and Governance (High-Level, Not Legal Advice)
### 7.1 Onboarding Flows
- **Retail Investors**: KYC (ID + selfie), AML (sanctions, PEP), suitability questionnaire (investment limits), geo-fencing by state; disclaimers on investment risk.
- **Accredited Investors**: Additional accreditation verification (income/net worth docs or third-party letter), higher limits, ability to join specific offerings.
- **Owners**: Business verification (EIN, entity docs), beneficial ownership disclosures per FinCEN requirements, property ownership proof.

### 7.2 Disclosures & Consent
- Display Private Placement Memorandum (PPM), Subscription Agreement, Risk Disclosure, Data Privacy policy, E-Sign consent, ACH authorization.
- Capture explicit consent (checkbox + timestamp); store signed agreements in Document table.

### 7.3 Jurisdictional Considerations
- Structure: Series LLC or SPV per property; nominee holding structure if needed for local regulations. Maintain beneficial ownership register accessible to regulators.
- Geofencing: Block jurisdictions where offerings not registered; IP + document check.
- Escrow: Funds held in segregated client trust accounts; follow NACHA/ACH rules.

### 7.4 Data Privacy & Security
- Data retention: Keep financial records 7 years; PII per legal requirement; support right-to-erasure except for regulatory retention.
- Encryption: PII, documents encrypted at rest; TLS 1.3 in transit; rotate keys quarterly via AWS KMS.

### 7.5 Monitoring & Fraud Prevention
- Velocity checks on commitments; device fingerprinting; sanctions re-screening daily.
- SAR escalation: Workflow for suspicious activity; compliance officer review; record decisions.

## 8. Detailed Algorithms & Pseudocode
### 8.1 Pro-Rata Ownership Allocation
```
function allocateOwnership(campaignId):
  commitments = fetchCommitments(campaignId, status='captured')
  totalRaised = sum(commitment.amount for commitment in commitments)
  hardCap = getCampaignHardCap(campaignId)
  if totalRaised > hardCap:
    scaleFactor = hardCap / totalRaised
  else:
    scaleFactor = 1
  for commitment in commitments:
    adjustedAmount = round(commitment.amount * scaleFactor, 2)
    ownershipPct = adjustedAmount / hardCap
    ownershipUnits = round(ownershipPct * 10000) / 10000
    residual = commitment.amount - adjustedAmount
    if residual > 0:
      queueRefund(commitment.id, residual)
    createOwnershipLot(commitment.investorId, ownershipUnits, ownershipPct)
    logAllocation(commitment.id, adjustedAmount, ownershipPct)
```

### 8.2 Distribution Engine
```
function executeDistribution(distributionId):
  distribution = getDistribution(distributionId)
  assert distribution.status == 'scheduled'
  ownershipLots = getOwnershipLots(distribution.campaignId)
  gross = distribution.grossAmount
  platformFee = round(gross * distribution.platformFeeRate, 2)
  taxWithheld = calculateTax(ownershipLots)
  netPool = gross - platformFee - taxWithheld
  for lot in ownershipLots:
    share = round(netPool * lot.percentage, 2)
    payout = createPayout(lot.id, share)
    enqueuePayout(payout.id)
  updateDistribution(distributionId, status='processing', fee=platformFee, tax=taxWithheld)
  logEvent('distribution_started', distributionId)
```

### 8.3 Refund Engine
```
function processRefunds(campaignId):
  campaign = getCampaign(campaignId)
  assert campaign.status in ['failed', 'refunded']
  commitments = getCommitments(campaignId, status in ['held', 'captured'])
  for commitment in commitments:
    refundAmount = commitment.amount - commitment.capturedFees
    response = paymentProvider.refund(commitment.paymentIntentId, refundAmount, idempotencyKey=commitment.id)
    if response.success:
      updateCommitment(commitment.id, status='refunded')
      recordEscrowTransaction(commitment.id, type='refund', amount=refundAmount)
    else:
      queueRetry(commitment.id, reason=response.error)
  markCampaignRefundedIfAll()
```

### 8.4 Local Discovery Ranking
```
function rankListings(userLocation, listings):
  for listing in listings:
    distanceScore = normalizeDistance(distance(userLocation, listing.geo))
    fundingScore = listing.fundedAmount / listing.hardCap
    riskScore = 1 - listing.riskRating  # lower riskRating better
    yieldScore = normalizeYield(listing.projectedYield)
    freshnessScore = normalizeTime(listing.goLiveAt)
    listing.rankScore = 0.35*distanceScore + 0.25*fundingScore + 0.2*yieldScore + 0.15*freshnessScore + 0.05*riskScore
  return sortBy(listings, 'rankScore', descending=True)
```

## 9. DevOps & Observability
### 9.1 Infrastructure as Code
- Terraform modules for VPC, subnets, security groups, RDS PostgreSQL, Redis (ElastiCache), ECS Fargate services (frontend SSR & backend API), S3 buckets, CloudFront CDN, IAM roles, Secrets Manager.
- Environments: Dev (shared), Staging (production-like, feature flags), Production (multi-AZ). Feature branches deploy to ephemeral preview via Vercel for frontend.
- CI/CD: GitHub Actions → lint/test → build Docker images → push to ECR → Terraform plan (manual approval) → deploy via blue-green (ECS). Database migrations via Prisma migrate with zero-downtime strategy (deploy applies with shadow table).
- Backups: Automated RDS snapshots daily, PITR enabled; S3 versioning; Redis snapshot hourly.

### 9.2 SLOs/SLIs
- Availability SLO 99.5% (API error budget ~3.65h/month). SLI: `1 - (5xx responses / total requests)`.
- Latency SLO: P95 <300ms for GET /campaigns; SLI measured via APM.
- Alerting: PagerDuty integration for SLO burn rate >2x; CloudWatch alarms for CPU/memory; synthetic monitoring.
- Runbooks: Documented steps for payment provider outage, KYC backlog, data breach, high-latency incidents.

## 10. Security
### 10.1 Threat Model (STRIDE)
- **Spoofing**: Mitigation via OAuth/OIDC, MFA optional, email verification.
- **Tampering**: Signed URLs, checksum on documents, WAF, immutability of audit logs.
- **Repudiation**: Comprehensive audit logs, non-repudiation via e-signatures, time-stamped actions.
- **Information Disclosure**: Encryption, access control, data minimization, secure coding.
- **Denial of Service**: Rate limiting, CDN, autoscaling, DDoS protection (AWS Shield).
- **Elevation of Privilege**: RBAC, least privilege IAM, periodic reviews, admin action approvals.

### 10.2 OWASP ASVS Mapping
- Authentication (V2): OIDC, passwordless, account lockouts.
- Session Management (V3): Secure cookies, rotation on privilege change.
- Access Control (V4): RBAC, policy checks in middleware, automated tests.
- Input Validation (V5): Zod/DTO validation, allowlist geolocation.
- Cryptography (V7): TLS 1.3, AES-256, key rotation.
- Error Handling (V10): No sensitive info in errors, correlation IDs.
- Data Protection (V9): PII classification, masking in logs.
- API Security (V13): Rate limits, JWT validation, idempotency.

### 10.3 Secrets & Access
- Secrets in AWS Secrets Manager; rotated quarterly; use IAM roles for ECS tasks.
- Least privilege: separate service accounts; manual access via SSO with MFA; quarterly review.
- Anomaly detection: AWS GuardDuty, CloudTrail alerts for unusual activity.

### 10.4 Pen-Test & Bug Bounty
- Annual third-party penetration test pre-launch; re-test after major releases.
- Bug bounty via HackerOne private program after 3 months of production.

## 11. Analytics & KPIs
### 11.1 North-Star & Supporting Metrics
- North-Star: **Net Invested Capital in Local Campaigns per Active Investor**.
- Activation: % investors completing first commitment within 7 days.
- Engagement: Time spent in property detail; watchlist additions.
- Retention: Cohort-based repeat investments at 30/60/90 days.
- Liquidity: Median time-to-fill; ratio of successful campaigns.
- Monetization: Average origination fee per campaign; take-rate vs. GMV.

### 11.2 Event Schema
| Event Name | Properties | Notes |
| --- | --- | --- |
| `listing_created` | `listing_id`, `owner_id`, `target_amount`, `geo` | Validate required fields. |
| `listing_verified` | `listing_id`, `verification_result` | Edge: verification_failed. |
| `investor_commitment` | `commitment_id`, `campaign_id`, `amount`, `payment_method_type`, `distance_km` | Ensure amount >= min. |
| `campaign_status_changed` | `campaign_id`, `from`, `to`, `reason` | Idempotent.
| `distribution_executed` | `distribution_id`, `gross_amount`, `net_amount` | Ensure net>=0.
| `kyc_status_changed` | `user_id`, `status`, `risk_score` | Mask PII.

### 11.3 Sample SQL Queries
- **Time-to-Fill**:
```
SELECT campaign_id,
       EXTRACT(EPOCH FROM (settled_at - start_at))/3600 AS hours_to_fill
FROM campaigns
WHERE status = 'successful';
```
- **Investor Activation Funnel**:
```
SELECT cohort_date,
       COUNT(DISTINCT user_id) FILTER (WHERE step = 'signup') AS signups,
       COUNT(DISTINCT user_id) FILTER (WHERE step = 'kyc_approved') AS kyc,
       COUNT(DISTINCT user_id) FILTER (WHERE step = 'first_commitment') AS activated
FROM investor_funnel
GROUP BY cohort_date;
```
- **Capital Velocity**:
```
SELECT DATE_TRUNC('week', start_at) AS week,
       SUM(funded_amount) / COUNT(*) AS avg_velocity
FROM campaigns
WHERE status IN ('successful','pending_settlement')
GROUP BY week;
```

## 12. Monetization & Unit Economics
- **Revenue Streams**: Listing fee ($500 per property), origination fee (2% of target on success), carry on exit profits (10%), assets under management fee (0.5% annually on outstanding equity), premium analytics subscription.
- **Pricing Experiments**: A/B test origination fee tiers (1.5%, 2%, 2.5%); loyalty discounts for investors; referral bonuses vs. fee credits.
- **Fee Sensitivity Tests**: Survey + analyze churn when fees adjusted; elasticity using cohort GMV.
- **Break-Even Analysis**: Fixed costs (team, infrastructure) vs. per-campaign variable costs (KYC, payment fees). Target GMV per month to cover costs; e.g., need $5M GMV with 2% fee to hit $100k revenue.
- **Marketplace Liquidity Targets**: Aim for 80% campaigns funded within 45 days; average commitments per investor 3+.

## 13. Go-to-Market Strategy
### 13.1 ICPs
- **Owners**: Independent landlords, small developers, inherited property holders.
- **Investors**: Millennials/Gen X with $5k-$100k investable assets, location-proud, ESG-conscious.
- **Partners**: Local real estate agents, community banks, municipalities seeking revitalization.

### 13.2 Acquisition Channels
- Local meetups, real-estate associations, content marketing (case studies), referral program (fee rebates), partnerships with title companies, targeted social ads with geo-fencing, PR in local business journals.

### 13.3 Trust Builders
- Publish due diligence methodology, third-party audits, proof-of-reserve for escrow, investor testimonials, compliance certifications.

### 13.4 Launch Checklist & Timeline
- **T-8 weeks**: Finalize legal structure, onboard KYC/payment vendors, recruit pilot owners.
- **T-6 weeks**: Build marketing site, launch waitlist, publish educational content.
- **T-4 weeks**: Complete MVP QA, integrate analytics, train compliance/admin.
- **T-2 weeks**: Soft launch with pilot investors, gather feedback, pen-test.
- **T-0**: Public launch webinar, PR release, activate referral program.
- **T+2 weeks**: Review KPIs, iterate on onboarding; start content drip.
- **T+4 weeks**: Expand to second city, evaluate roadmap features (secondary market).

## 14. Implementation Plan
### 14.1 12-Week MVP Roadmap
- **Week 1-2**: Requirements refinement, architecture setup, DevOps baseline, authentication integration.
- **Week 3-4**: Owner listing flows, property data models, document upload.
- **Week 5-6**: Investor discovery, map integration, commitment flow (escrow sandbox).
- **Week 7-8**: Campaign lifecycle automation, compliance dashboard, notifications.
- **Week 9-10**: Distribution module, payout integration, reporting.
- **Week 11**: Accessibility testing, localization, performance tuning.
- **Week 12**: End-to-end QA, security review, go-live readiness.

### 14.2 RACI & Staffing
- **Product**: PM (Responsible), CTO (Accountable), Design Lead (Consulted), Compliance Officer (Consulted), Engineering Leads (Responsible), QA (Responsible), Marketing (Informed).
- Engineering team: 2 frontend, 2 backend, 1 full-stack, 1 DevOps, 1 data analyst, 1 QA, 1 compliance specialist, fractional legal counsel.

### 14.3 Risk Burndown
- Track weekly risk register updates; integrate gating criteria (KYC vendor SLA, escrow approval). Use burndown chart of high-risk items to 0 by week 10.

### 14.4 Ticket-Level Backlog (Sample)
- **Epic: Owner Onboarding**
  - Story: Implement owner signup + KYC start. Tasks: Auth0 configuration; KYC API integration; UI form validation.
  - Story: Property listing wizard. Tasks: Stepper UI; backend DTO; PostGIS integration; file upload.
- **Epic: Investor Commitment**
  - Story: Map-based discovery. Tasks: Mapbox integration; filter API; accessibility audit.
  - Story: Commitment payment intent. Tasks: Stripe API wrapper; idempotency middleware; escrow ledger entries.
- **Epic: Compliance Console**
  - Story: Review queue UI. Tasks: Table component; GraphQL query; decision endpoints.
- **Epic: Distribution Engine**
  - Story: Admin create distribution. Tasks: Form; allocation preview; payout execution.
- **Definition of Ready (DoR)**: User story with persona, acceptance criteria, designs, data requirements, dependencies resolved.
- **Definition of Done (DoD)**: Code merged with tests, documentation updated, feature toggled, monitoring alerts configured, stakeholder sign-off.

## 15. Example Data & Test Plan
### 15.1 Seed Dataset
- **Cities**: Austin, TX; Atlanta, GA; Denver, CO.
- 20 properties sample (IDs `prop_a1` ... `prop_d7`) with fields: address, geo coordinates, property_type, valuation, rent_yield, documents, verification_status.
  - Example: `prop_a1`: 123 Congress Ave, Austin, lat 30.2672, lng -97.7431, type `residential`, valuation $450k, min_ticket $2k, target $150k.
  - Include mix of statuses (draft, active, successful, failed) to test flows.

### 15.2 Test Cases
- **Functional**: Owner listing creation validation (min ticket > target error); KYC rejection loop; investor commitment with oversubscription; campaign settlement with payment failure fallback; distribution with tax withholding; refund idempotency.
- **Edge**: Payment chargeback handling; investor from blocked state; property delisted after commitments; duplicate document upload; time zone boundary for deadlines.
- **Security**: AuthZ (owner cannot view other owner's payouts), SQL injection tests, IDOR, rate limiting.
- **Load**: Simulate 5k concurrent investors committing; API throughput 1k req/s bursts; map tiles caching.
- **QA Plan**: Automated unit/integration tests, contract tests with vendors, manual exploratory on staging, accessibility audits (axe), localization review.
- **UAT Checklist**: End-to-end owner raise, investor commitment, settlement, distribution, refund scenario, admin approvals; pass if no P0/P1 defects and compliance sign-off.

## 16. Assumptions, Risks, Open Questions
- **Assumptions**: Operating primarily in US markets; securities exemptions available via Reg CF/Reg D; Stripe supports escrow in target regions; Persona integration SLA met; investors comfortable with digital-only onboarding.
- **Risks**: Regulatory changes affecting fractional ownership; payment provider limitations; real estate market downturn reducing deal flow.
- **Open Questions**: Which states launch first (affects compliance)? Will platform handle property management or integrate partners? What is tax reporting obligation per jurisdiction? Secondary trading timeline relative to regulatory approvals?

*Disclaimer: This document is for planning purposes only and does not constitute legal or financial advice. Consult qualified professionals before execution.*
