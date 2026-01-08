# Serveqrew Join The Waitlist Backend.

A production-ready waitlist signup API built with Deno, Supabase, and Resend. Handles user signups, referrals, rate limiting, email verification, and Supabase magic links with robust error handling.

## ✨ Features.

- **Rate Limiting**: 5 requests per IP every 2 minutes.
- **Referral System**: Auto-generates unique referral codes and tracks referral counts.
- **Email Integration**: Sends personalized welcome emails with referral links + Supabase magic links via Resend.
- **CORS Support**: Production-ready CORS headers.
- **Comprehensive Validation**: Input sanitization, length checks, email format validation.
- **Error Handling**: Granular HTTP status codes with user-friendly messages.
- **Duplicate Prevention**: Blocks duplicate email signups.
- **Supabase RLS Ready**: Uses service role key for secure database operations.

## 🛠 Tech Stack.

- **Runtime**: Deno 0.177.0
- **Database**: Supabase (PostgreSQL).
- **Email**: Resend.
- **Auth**: Supabase Magic Links.

## 🚀 Quick Start.

### 1. Environment Variables.
```bash
SUPABASE_URL=your_supabase_project_url
SUPABASE_SERVICE_ROLE_KEY=your_service_role_key
RESEND_API_KEY=your_resend_api_key
```

### 2. Deploy (Supabase Edge Functions).
```bash
# Deploy to Supabase Edge Functions.
supabase functions deploy join-waitlist
```

### 3. Test Endpoint.
```bash
curl -X POST https://your-project.supabase.co/functions/v1/join-waitlist \
  -H "Content-Type: application/json" \
  -d '{
    "full_name": "John Doe",
    "email": "john@example.com",
    "brand_name": "My Brand" // optional field.
  }'
```

## 📋 API Reference.

### `POST /join-waitlist`

**Request Body**
```ts
{
  full_name: string,    // Required, max 35 chars.
  email: string,        // Required, valid email format.
  brand_name?: string,  // Optional, max 50 chars.
  ref?: string          // Optional referral code.
}
```

**Success Response (201)**
```json
{
  "message": "You have successfully joined the waitlist🤓. A confirmation email with your referral and dashboard link has been sent to you.🫶"
}
```

**Error Responses**
| Status | Error | Description |
|--------|--------|-------------|
| `429` | `rate_limit_exceeded` | Too many requests |
| `409` | `duplicate_entry` | Email already exists |
| `422` | `invalid_email` | Invalid email format |
| `400` | `invalid_request_body` | Validation failed |
| `415` | `invalid_content_type` | Missing JSON Content-Type |

## 🗄 Database Schema.

```sql
-- waitlist_signups table
CREATE TABLE waitlist_signups (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  full_name text NOT NULL,
  email text UNIQUE NOT NULL,
  brand_name text,
  referral_code uuid UNIQUE NOT NULL,
  referred_by text,
  referral_count INTEGER DEFAULT 0,
  created_at TIMESTAMP DEFAULT NOW()
);

-- waitlist_rate_limits table (assumed)
CREATE TABLE waitlist_rate_limits (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  ip INET NOT NULL,
  endpoint VARCHAR NOT NULL,
  created_at TIMESTAMP DEFAULT NOW()
);
```

## 🔒 Security Features.

- ✅ Rate limiting per IP.
- ✅ Email uniqueness constraint.
- ✅ Input sanitization & validation.
- ✅ Service role key (bypasses RLS safely).
- ✅ CORS restricted to production domain.
- ✅ Comprehensive error handling (no stack traces leaked).

## 📧 Email Template.

Users receive a beautiful HTML email containing:
- Personalized welcome message.
- Their unique referral link.
- Supabase magic link for dashboard access.
- Contact saving instructions.

## 🧪 Robust Error Handling.

```
invalid_content_type (415) → Missing JSON header
invalid_request_body (400) → Field validation failed  
invalid_email (422) → Bad email format
rate_limit_exceeded (429) → Too many requests
duplicate_entry (409) → Email exists
magic_link_error (503) → Supabase auth issue
email_quota_exceeded (501) → Resend daily limit
email_send_failed (502) → Email delivery failed
```

## 📈 Production Ready

- Graceful degradation (email failures don't block signup).
- Fail-open rate limiting.
- Proper HTTP status codes.
- Structured logging.
- Zero-downtime deployable.

## 🤝 Contributing.

1. Fork the repo.
2. Create feature branch (`git checkout -b feature/amazing-feature`).
3. Deploy to test Supabase project.
4. Test thoroughly (rate limits, duplicates, referrals).
5. PR with clear description.

***

**Built for Serveqrew by [Okoli Ebube]**

***
