# Legal Contract Risk Analyzer — Structured Output + Citation Verification

An AI contract reviewer that can't make up quotes. Every risk it reports comes with an exact citation from the contract, checked in code before it's shown to you.

**Try it yourself → [Open in Colab](https://colab.research.google.com/) | Just run all cells and paste a free [Groq](https://console.groq.com) API key.**

## Problem
In legal work, a made-up answer is dangerous, not just annoying. A normal chatbot writes free text and can invent clauses that don't exist. I wanted the AI to act like a deterministic extraction engine instead — fixed schema, no ambiguity, and every claim traceable back to the source document.

## Approach
- Defined a strict Pydantic schema (clause title, risk level, verbatim quote, explanation, suggested redline) so the model can't return free-form text
- Built a retry loop: if the JSON breaks validation, the error is sent back to the model to fix
- Added a citation verifier in plain Python — every `verbatim_quote` is checked as an exact substring of the real contract text
- If a quote is fake, it's flagged back to the model by name and the model has to retry
- Tested on a vendor contract where I planted 4 real-world unfair clauses on purpose (one-sided indemnity, low liability cap, vendor-favored termination, vendor owning all customer data), so I could check if the AI actually catches them

## Result (from my own run)
| Metric | Result |
|---|---|
| Planted risky clauses caught | 4 / 4 |
| Findings returned | 6, all quote-verified |
| Fake / unverifiable quotes caught & removed | 0 (passed validation on attempt 1) |
| Overall contract risk score | 80 / 100 |

Sample finding:
> **Clause: Limitation of Liability — CRITICAL**
> Quote: *"Vendor's total liability under this Agreement shall not exceed the fees paid by Customer in the one month before the claim arose... Customer's liability is unlimited."*
> Why it's risky: Vendor's liability cap is extremely low and excludes core damages, while the Customer carries unlimited liability.
> Suggested redline: Limit Vendor's liability to at least 12 months of fees paid, and cover data loss and business interruption.

## Tech Stack
Python · Pydantic · Groq API (`openai/gpt-oss-120b`, JSON mode) · pandas · matplotlib

## Why this matters
This is Project 2 of a 5-project Agentic AI series, each one building on the last one's weak point: RAG → structured/audited outputs (this one) → autonomous tool-calling agents → multimodal (vision) RAG → real-time Corrective RAG with routing and caching.

Note: this is a learning project, not legal advice.

---
**Akshat Kesharwani** — Fresher Data Analyst / Data Scientist
Portfolio: https://akshatkesharwani-info.github.io/ | GitHub: https://github.com/akshatkesharwani-info
