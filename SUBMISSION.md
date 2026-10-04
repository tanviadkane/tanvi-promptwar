# Submission — The Blind Spot

**Working link:** https://revolutionary-medline-edinburgh-touch.trycloudflare.com *(tunnel; stable hosting via `gcloud run deploy` per README)*
**Repo:** this folder (`git init` done, single commit)

## Brief description

**Blind Spot** helps people think more critically about **any** decision they're facing — a job offer, a course, a big purchase, a move, a relationship. Users describe the decision, their situation, and the reasons they're leaning the way they are. An AI review (Gemini, structured JSON, schema-validated) then shows how their reasoning looks from the outside: a neutral mirror, blind spots grouped as *overlooked factors, unstated assumptions, conflicts in their own reasoning, and missing perspectives*, plus assumptions to test, questions for themselves, and questions to ask the people involved.

Two deliberately **non-AI** layers keep the tool grounded: a coverage map computed from the user's own words (which decision factors — money, time, risk, alternatives, people… — their reasons never mention) and a domain-specific verification checklist. A code-level guardrail rejects any AI output containing recommendation language ("you should…", "the best option…") and retries, so the product structurally *cannot* decide for the user — matching the core constraint of the brief. Nothing is stored: no database, no login, reflection notes stay in the browser.

**Meaningful AI use:** prompt-injection-hardened structured generation with schema validation, retry-on-unsafe-output guardrail, and domain-adaptive analysis — not a chat wrapper.

**Testing:** 38 unit/integration tests (validation, sanitisation, guardrail, coverage, checkpoints, live-mocked Gemini flow, HTTP security headers, rate limiting); manually verified end-to-end in the browser, including the guardrail against "tell me what to choose" injections.
