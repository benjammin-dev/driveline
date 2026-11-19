# ADR-0008: Receipt scanning with Amazon Textract AnalyzeExpense

- **Status:** Proposed
- **Date:** 2026-11-19
- **Related:** FR-071

## Context

Pro users photograph fuel and service receipts and expect the form to be filled in automatically: date, total, volume, unit price and station.

## Options considered

1. **On-device OCR** (ML Kit / Apple Vision) + our own parsing — free and offline; we must write receipt-understanding logic ourselves.
2. **Amazon Textract `AnalyzeExpense`** — purpose-built for receipts and invoices; returns labelled fields (vendor, total, date) and line items; pay per page; stays inside AWS.
3. **General-purpose LLM vision API** — flexible; higher and less predictable cost, and data leaves AWS.

## Decision

Use **Textract `AnalyzeExpense`** called from a Lambda in us-west-2, behind an `ReceiptScanner` interface. Map Textract's summary fields to our form; extract volume and unit price from line items with domain parsing rules tested against a labelled receipt set.

## Consequences

- Good accuracy on totals, dates and vendor out of the box; fuel volume may need custom parsing.
- Per-scan cost covered by the Pro subscription; rate-limit to 100 scans per user per month.
- Images are stored privately in S3 and deleted with the account. Option 1 can be added later as an offline fallback.
