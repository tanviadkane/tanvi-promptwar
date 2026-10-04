# Blind Spot

**See what your reasoning misses.** Blind Spot is an AI-powered decision-reflection companion for **any** decision — a job offer, a course, a big purchase, a move, a relationship, anything with trade-offs. It analyses *how* you're reasoning, surfaces blind spots, and hands you better questions.

**It never decides for you.** There is no "accept / reject" verdict anywhere in the product — a code-level guardrail rejects any model output that contains recommendation language and retries.

## How it works

1. **Describe the decision** — what you're deciding, optional options and deadline, your situation and constraints, and the reasons you're leaning the way you are.
2. **AI blind-spot review (Gemini)** returns structured, validated JSON:
   - a neutral *mirror* of your reasoning,
   - **blind spots** in four categories (overlooked factor, unstated assumption, conflict in reasoning, missing perspective) with severity,
   - **assumptions to test**, **conflicts**, **questions to ask yourself**, **questions to ask others**.
3. **Computed in code, not by AI** — two grounded layers run without the model:
   - a *coverage map*: which decision factors (money, time, goals, learning, wellbeing, people, risk, alternatives) your own reasons actually mention;
   - a *verification checklist* tailored to the decision domain (career, money, purchase, relocation, health, …).
4. You finish with a **reflection box** — notes stay in your browser; nothing is stored server-side.

## Meaningful use of AI

- Structured-output call to Gemini (JSON mode, temperature 0.3, 20 s timeout) with **Zod schema validation** and one retry on malformed or unsafe output.
- **Prompt-injection defence**: user text is wrapped in an untrusted `<user_data>` block with explicit ignore-instructions rules.
- **"Never decides" guardrail**: regex verdict-language detector runs on every model output; a hit triggers a corrective retry, then a safe error — verified by unit tests.

## Security

- Helmet with strict CSP (`default-src 'self'`, `frame-ancestors 'none'`, no inline scripts), `no-referrer`.
- Zod validation + deep sanitisation (angle brackets, control chars) on all input; 10 kB body limit.
- Rate limit: 10 requests/min/IP. Responses cached in-memory (10 min TTL) — **no database, nothing persisted**.
- API key lives only in `.env` / Cloud Run secret. `.env` is gitignored.

## Accessibility

Semantic landmarks, skip link, `aria-live` step announcements, `role=alert` errors, labelled fields with hint/error wiring, visible focus rings, 2.75rem touch targets, `prefers-reduced-motion` support, light/dark via `prefers-color-scheme`.

## Stack

React 18 + TypeScript + Vite · Express · Zod · `@google/genai` · Vitest (38 tests) · Dockerfile for Cloud Run / any container host.

## Run locally

```bash
npm install
cp .env.example .env    # add your GEMINI_API_KEY (Google AI Studio)
npm run dev             # client on :5173, server on :8080
npm test                # 38 tests
npm run build && npm start   # production: single server on :8080
```

## Deploy (Google Cloud Run)

```bash
gcloud run deploy blind-spot --source . --region asia-south1 \
  --allow-unauthenticated --set-secrets GEMINI_API_KEY=gemini-key:latest
```

Any container host works the same way (the Dockerfile builds the client and runs the server on :8080).

## Honest limits

- AI output can still miss things — that's why the deterministic coverage map and checklist exist, and why every screen reminds you the decision stays with you.
- In-memory cache is per-instance; rate limits reset each minute. Fine for a demo, not multi-region scale.
