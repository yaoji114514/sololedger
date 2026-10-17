# DEV submission draft — Hacktoberfest 2026, Week 2: Side Hustle
# Post this on dev.to with the challenge's submission template before
# Oct 18, 2026 11:59 PM PDT (Oct 19, 2:59 PM Beijing).
# Replace [REPO LINK] with your GitHub repo URL.

---
title: "SoloLedger: a freelance finance tool that runs 100% on your machine"
tags: hf26challenge, opensource, ai, sidehustle
---

**TL;DR:** I built a tool that turns messy PayPal/Wise/Upwork CSV exports into a clean monthly finance report — categorized by a *local* open-weight model, so your money data never leaves your laptop. Repo: [REPO LINK]

## The problem

I'm a freelancer getting paid across Upwork, PayPal, and direct transfers. At the end of every month I face the same archaeology dig: five CSV formats, fees buried in payout lines, no idea which income has an invoice behind it, and a vague sense I should be setting tax money aside.

The existing answers are all cloud SaaS: connect your accounts, upload everything, trust us. For *financial data*, that's a non-starter for a lot of people — and it's exactly where open-source AI changes the deal.

## What I built

**SoloLedger** — drop in your CSV exports, get a monthly report:

- Income/expenses by category, per-currency totals (currencies are never mixed — a finance tool that adds USD and EUR together is lying to you)
- Top income sources, invoice-matching gaps ("$850 from Upwork with no invoice reference — follow up")
- A suggested tax reserve
- **Receipt capture**: snap a photo of a paper receipt and it lands in the report as an expense — read on-device by a local vision model (`qwen2.5-vl`), with tesseract OCR as fallback, so the privacy story holds for paper too

The pipeline is four small pieces: fuzzy CSV ingestion (PayPal's `Net` vs. Wise's `Amount` just work), receipt photo capture, categorization, and reporting to Markdown + a standalone HTML page. The CLI is **pure Python stdlib — zero dependencies**, and there's an optional Flask web UI.

## Why open-source AI is the core, not the garnish

Categorization is where the model earns its keep. Real freelancer transaction descriptions are feral: `"old client — thanks for the rush job!!"`, `"stripe payout *acme rebrand phase 2"`. A keyword list rots within a month; a small local model (`qwen2.5:3b` via Ollama) handles the long tail, and when no model is installed, offline rules take over so the tool never breaks.

But the deeper point is architectural: **the open model is the privacy story**. A SaaS competitor can never say "your data never leaves your machine," because their model lives in their cloud. Mine lives on your laptop. Open weights aren't a hackathon constraint I worked around — they're the reason this product can exist in a form people trust with their money.

## Who would pay for this

Solo freelancers and micro-agencies doing $2k–$20k/month across platforms — especially cross-border ones juggling USD/EUR/CNY, which is half of Upwork at this point. The pitch writes itself: *"it does your monthly books in 10 seconds and never uploads anything."* At $8/month that's an impulse buy against the $30/month SaaS that wants your bank login. Boring, specific, real — that's the whole side-hustle thesis.

## Try it

```bash
git clone [REPO LINK]
cd sololedger
python -m sololedger   # or double-click start.sh / start.bat
# your browser opens: drag in a CSV (or a receipt photo), get the report.
# sample data included, so it works before you even export your own CSV.
# full AI mode: install Ollama, `ollama pull qwen2.5:3b` — auto-detected.
# one-file binaries (no Python needed) ship via GitHub Releases.
```

## What's next

Direct PayPal/Wise API import (still local-first — keys in env vars only, traffic straight from your machine) is already in, plus a phone-upload API. Next up: a CNY tax-report export. If any of that sounds like your month-end pain too, I'd genuinely like to hear what your CSVs look like — the fuzzy parser gets better with every weird format it meets.
