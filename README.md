# Collab Payment & Payee PRD

Version: v1.1
Date: 2026-07-02
Platform: Airacle Collab (`/home/oyzh888/collab-payment-git`)
Scope: Creator collaboration payment tracking, payee profile, receiving account, and social account verification
Status: Platform-specific rewrite replacing the generic wallet / withdrawal PRD

## 1. Product Positioning

Collab Payment is not a stored-value wallet, consumer wallet, or creator-initiated withdrawal system.

It is the payment operations layer for Airacle's creator collaboration workflow. It tracks collaboration fees owed to creators after a campaign match is confirmed, helps creators provide payee information, and lets Admin record payment progress.

V0 payment execution happens outside Collab. Admin records payment status manually in the platform.

Core flow:

```text
CampaignMatch confirmed
-> Admin creates creator payment
-> Creator completes payee profile and receiving account
-> Payment becomes ready to pay
-> Admin marks payment as invoiced / paid / failed
-> Creator sees payment progress in Payee Center
-> Activity is retained for operations review
```

## 2. Existing Collab Context

The current Collab platform already includes:

- Brand Client management.
- Campaign creation and management.
- Creator Pipeline.
- Campaign Match Board.
- `CampaignMatch.confirmedPrice`.
- Admin Payment Tracker demo at `/admin/payments`.
- Payee Portal demo at `/portal/payee`.
- YouTube social OAuth demo.
- Activity logs.

The payment PRD should extend these existing objects instead of introducing a standalone wallet product.

## 3. Product Goals

1. Admin can create and manage payment records from confirmed campaign matches.
2. Creator can complete personal/payee information required for receiving collaboration payments.
3. Creator can add a SWIFT receiving account.
4. Creator can verify social accounts used for collaboration eligibility.
5. Admin can see whether a payment is blocked by missing payee information.
6. Admin can manually update payment status and retain an audit trail.
7. Creator can view pending, paid, and failed payment records.

## 4. Non-Goals

V0 does not include:

- Creator-initiated withdrawals.
- Stored wallet balance.
- Wallet recharge.
- User-to-user transfer.
- Balance freeze / unfreeze ledger.
- Real payment gateway execution.
- Automatic PayPal, Airwallex, or bank payout API calls.
- Full reconciliation center.
- Tax reporting.
- Invoice generation.
- Multi-currency FX conversion.
- Complex finance approval workflow.
- General-purpose financial ledger.

These may become future finance features after the basic payment operations workflow is validated.

## 5. Users

### 5.1 Admin / Operator

Internal Airacle user responsible for campaign operations and creator payment tracking.

Primary needs:

- Know which confirmed creator collaborations need payment.
- See whether creator payee information is complete.
- Record invoice/payment progress.
- Mark payment success or failure.
- Leave notes for operations and finance follow-up.

### 5.2 Creator / Payee

External creator or collaboration participant.

Primary needs:

- Complete personal information.
- Verify collaboration-related social accounts.
- Add receiving account information.
- See expected and completed collaboration payments.
- Understand payment failure reasons and required next actions.

### 5.3 Finance

Future or lightweight V0 internal role. In V0, finance actions can be represented by Admin/Operator.

Primary needs:

- Confirm payment amounts and recipient details.
- Track manual payment status.
- Review payment history.

## 6. Information Architecture

### Admin

```text
Admin
├── Campaign Management
├── Match Board
└── Payment Tracker
    ├── Payment list
    ├── Payment detail
    ├── Create from confirmed match
    ├── Payee readiness
    └── Payment status update
```

### Creator

```text
Payee Center
├── Account Verification
├── Personal Info
├── Receiving Account
└── Payments
    ├── Pending payment
    ├── Paid
    └── Failed / Action Required
```

`/portal/payee` should be treated as Payee Center, not a full wallet.

## 7. Data Model

### 7.1 Existing Objects To Reuse

- `users`
- `creator_profiles`
- `social_accounts`
- `brand_clients`
- `campaigns`
- `campaign_matches`
- `activity_logs`

### 7.2 New Recommended Objects

#### `payee_profiles`

One per creator profile.

Fields:

| Field | Description |
| --- | --- |
| `id` | Payee profile ID |
| `creator_profile_id` | Linked creator |
| `user_id` | Linked auth user, if available |
| `legal_name` | Payee legal or beneficiary name |
| `phone_code` | Phone country code |
| `phone_number` | Phone number |
| `country` | Country/region |
| `province` | Province, if applicable |
| `city` | City |
| `state` | State, if applicable |
| `address` | Detailed address |
| `status` | `incomplete`, `complete`, `needs_review`, `rejected` |
| `created_at` | Created timestamp |
| `updated_at` | Updated timestamp |

#### `payee_accounts`

Receiving accounts for payments.

Fields:

| Field | Description |
| --- | --- |
| `id` | Account ID |
| `payee_profile_id` | Linked payee profile |
| `method` | `swift_bank` for V0 |
| `beneficiary_name` | Beneficiary name |
| `country` | Beneficiary country |
| `currency` | Account currency |
| `bank_name` | Bank name |
| `bank_country` | Bank country |
| `account_number_encrypted` | Encrypted account number or IBAN |
| `account_last4` | Last 4 digits for display |
| `swift_bic` | SWIFT/BIC code |
| `bank_address` | Bank address |
| `beneficiary_address` | Beneficiary address |
| `status` | `draft`, `submitted`, `verified`, `rejected`, `frozen`, `disabled` |
| `rejection_reason` | Reason if rejected |
| `created_at` | Created timestamp |
| `updated_at` | Updated timestamp |

V0 may use manual verification. Automatic bank account verification is out of scope.

#### `social_verifications`

Social account verification records.

Fields:

| Field | Description |
| --- | --- |
| `id` | Verification ID |
| `creator_profile_id` | Linked creator |
| `platform` | `youtube`, `tiktok`, `instagram`, `facebook`, `twitch`, `twitter`, `other` |
| `method` | `oauth`, `screenshot` |
| `external_account_id` | Provider account/channel ID if available |
| `account_name` | Channel/account name |
| `account_url` | Public profile URL |
| `status` | `pending`, `verified`, `failed`, `revoked` |
| `failure_reason` | Reason if failed |
| `verified_at` | Verified timestamp |
| `created_at` | Created timestamp |
| `updated_at` | Updated timestamp |

#### `creator_payments`

The core V0 payment object.

Fields:

| Field | Description |
| --- | --- |
| `id` | Payment ID |
| `campaign_match_id` | Linked campaign match |
| `campaign_id` | Denormalized campaign ID for filtering |
| `creator_profile_id` | Creator to be paid |
| `brand_client_id` | Client, if available |
| `payee_account_id` | Receiving account used |
| `amount_cents` | Payment amount in minor units |
| `currency` | Currency, default USD |
| `status` | Payment status |
| `source_amount_snapshot` | Confirmed match price at creation |
| `note` | Admin note |
| `failure_reason` | Failure reason if failed |
| `paid_at` | Paid timestamp |
| `created_by_user_id` | Admin who created payment |
| `updated_by_user_id` | Last updater |
| `created_at` | Created timestamp |
| `updated_at` | Updated timestamp |

#### `payment_events`

Append-only status and note history for payments.

Fields:

| Field | Description |
| --- | --- |
| `id` | Event ID |
| `payment_id` | Linked payment |
| `actor_user_id` | Actor, if available |
| `event_type` | `created`, `status_changed`, `note_added`, `payee_account_changed` |
| `from_status` | Previous status |
| `to_status` | New status |
| `metadata` | JSON metadata |
| `created_at` | Created timestamp |

## 8. Payment Status Model

Payment status should reflect Collab's operations workflow, not a general wallet withdrawal lifecycle.

```text
draft
-> pending_payee_info
-> ready_to_pay
-> invoiced
-> paid
-> failed
-> canceled
```

Status definitions:

| Status | Meaning | Creator Visible |
| --- | --- | --- |
| `draft` | Admin created payment but information is incomplete | No |
| `pending_payee_info` | Creator must complete profile/account before payment can proceed | Yes |
| `ready_to_pay` | Payee information is complete and Admin can proceed | Yes |
| `invoiced` | Payment has entered finance/manual payment handling | Yes |
| `paid` | Admin confirmed payment completed | Yes |
| `failed` | Payment failed and needs follow-up | Yes |
| `canceled` | Payment is canceled because collaboration no longer requires payment | Optional |

Allowed transitions:

| From | To |
| --- | --- |
| `draft` | `pending_payee_info`, `ready_to_pay`, `canceled` |
| `pending_payee_info` | `ready_to_pay`, `canceled` |
| `ready_to_pay` | `invoiced`, `canceled` |
| `invoiced` | `paid`, `failed`, `canceled` |
| `failed` | `ready_to_pay`, `canceled` |
| `paid` | no normal transition in V0 |
| `canceled` | no normal transition in V0 |

`paid` should be treated as terminal in V0. If a paid record was wrong, Admin should create an audit note or future reversal flow rather than editing it silently.

## 9. Payment Creation Rules

Admin can create a payment from a `CampaignMatch` when:

1. Match status is `client_confirmed`, `content_pending`, or `collaborated`.
2. Match has `confirmedPrice` or Admin enters a payment amount manually.
3. Creator profile exists.
4. There is no active payment for the same `campaign_match_id`, unless Admin explicitly creates an additional payment in a future split-payment feature.

Initial status:

- If creator has no complete payee profile or verified receiving account: `pending_payee_info`.
- If creator payee data is complete: `ready_to_pay`.

Amount rule:

```text
Default payment amount = CampaignMatch.confirmedPrice
```

If Admin overrides the amount, the UI must require a note.

## 10. Admin Payment Tracker Requirements

### 10.1 Payment List

Required columns:

- Creator.
- Campaign.
- Brand/client.
- Amount.
- Currency.
- Status.
- Payee readiness.
- Payment account summary.
- Last updated.
- Admin note.

Required filters:

- Status.
- Campaign.
- Creator.
- Payee readiness.
- Date range.

### 10.2 Create Payment

Admin should be able to:

- Select a confirmed match.
- Review creator, campaign, and confirmed price.
- Create a payment record.
- See whether payee info is missing.

### 10.3 Status Update

Admin can update:

- `ready_to_pay -> invoiced`
- `invoiced -> paid`
- `invoiced -> failed`
- `failed -> ready_to_pay`
- eligible statuses -> `canceled`

Failure requires a reason.

Paid requires:

- Payment date.
- Optional external reference number.
- Optional note.

Every status change must write `payment_events` and `activity_logs`.

## 11. Payee Center Requirements

### 11.1 Personal Info

Creator can maintain:

- Name.
- Phone number.
- Country/region.
- Province/city/state where relevant.
- Address.

V0 validation:

- Name required.
- Country required.
- Address required before account can be submitted.

Personal Info is not full KYC in V0. The UI should not imply government identity verification unless that process exists.

### 11.2 Account Verification

Creator can verify social accounts.

Supported V0:

- YouTube OAuth.
- Screenshot fallback for all platforms.

Rules:

- Same platform can have multiple accounts in the future, but V0 may show one primary verified account per platform.
- OAuth verification should save provider account ID, name, URL, method, status, and timestamp to D1.
- Screenshot verification creates `pending` status and requires Admin review in a later iteration.

OAuth privacy requirement:

- The UI must match the actual OAuth scopes requested.
- If the system only needs channel ownership, request only YouTube readonly scope.
- If analytics access is requested later, it must be explained separately and should not be presented as simple account verification.

### 11.3 Receiving Account

Creator can add a SWIFT receiving account.

V0 fields:

- Beneficiary name.
- Country.
- Transfer method: SWIFT.
- Account currency.
- Bank name.
- Bank country/region.
- Account number or IBAN.
- SWIFT/BIC code.
- Bank address.
- Beneficiary address.

Rules:

- Account number/IBAN must be encrypted at rest.
- UI shows only masked account details.
- Creator can have one active receiving account in V0.
- Admin can see masked details by default.
- Full sensitive details, if supported, must require elevated permission and audit logging.

### 11.4 Payments View

Creator sees payment records grouped by:

- `Pending Payment`: `pending_payee_info`, `ready_to_pay`, `invoiced`.
- `Paid`: `paid`.
- `Action Required`: `failed`.

Creator should not see internal Admin notes unless explicitly marked creator-visible.

## 12. Security And Compliance

Minimum V0 requirements:

- Creator can only view their own Payee Center.
- Admin APIs must enforce server-side authorization.
- Sensitive receiving account fields must be encrypted at rest.
- Account numbers must be masked in UI.
- OAuth must use `state` to prevent CSRF.
- OAuth tokens should not be stored unless needed. If stored, define retention and revocation behavior.
- Every payment status change must have an audit trail.

## 13. Migration From Current Demo

Current implementation gaps:

- `/admin/payments` uses localStorage demo data.
- `/portal/payee` uses localStorage for personal info, social verification, and receiving account data.
- D1 schema has no payment, payee account, or social verification tables.
- Payment status currently uses `pending`, `invoiced`, `paid`, `failed`, `refunded`; V0 platform workflow needs payee readiness states.

Migration plan:

1. Add D1 tables for payee profiles, payee accounts, social verifications, creator payments, and payment events.
2. Replace localStorage payment tracker with D1-backed Admin APIs.
3. Replace localStorage Payee Center with authenticated Creator APIs.
4. Connect payment creation to `campaign_matches`.
5. Add activity logging for payment creation and status changes.
6. Update Payee Center copy from wallet/withdrawal language to payment/receiving account language.

## 14. Acceptance Criteria

V0 is acceptable when:

1. Admin can create a payment from a confirmed campaign match.
2. Payment amount defaults from match confirmed price.
3. If payee info is missing, payment status becomes `pending_payee_info`.
4. Creator can save personal info to D1.
5. Creator can add a SWIFT receiving account to D1.
6. Receiving account is masked in UI.
7. Creator can complete YouTube OAuth verification and the result persists in D1.
8. Admin can move a payment through `ready_to_pay`, `invoiced`, `paid`, and `failed`.
9. Failed payment requires a failure reason.
10. Paid payment records payment date.
11. Creator can see pending, paid, and failed payment records.
12. Creator cannot see other creators' payment records.
13. Admin payment status changes create payment events and activity logs.
14. Refreshing the browser does not lose payment, payee, or verification data.

## 15. Later Versions

### V1

- Admin review for screenshot verification.
- Multiple receiving accounts.
- PayPal receiving account.
- Payment reference number and attachment upload.
- Basic export for finance.

### V2

- Real payment provider integration.
- Provider webhook status sync.
- Reconciliation import.
- Payment retry workflow.
- Finance role separation.

### V3

- Full wallet balance.
- Creator-initiated withdrawals.
- Ledger with freeze/unfreeze semantics.
- Tax and compliance workflows.
- Multi-currency FX handling.
