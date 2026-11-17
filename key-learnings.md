# Key Learnings — AI in the Modern SOC

## 1. Where AI can assist a SOC

AI can support several parts of a SOC workflow:

- **Detection engineering:** helping draft or explain detection logic for an engineer to review and test.
- **Alert triage:** organising alert context, grouping related items, and suggesting where an analyst might investigate next.
- **Investigation and enrichment:** helping formulate searches and organise relevant host, user, and event context.
- **Playbooks and response:** presenting possible next steps within the limits of approved workflows and permissions.
- **Case documentation:** preparing summaries, organising evidence references, and drafting handovers.

The presence of AI does not remove the need for an analyst to review the underlying evidence or approve consequential actions.

## 2. Understand what produced the result

Not every automated result is AI-generated. It is useful to distinguish between:

- **Detection analytics:** configured rules evaluate telemetry and generate alerts when their conditions match.
- **Deterministic automation:** predefined code runs known steps in a defined sequence.
- **Language-model output:** a model generates text from the context supplied to it.
- **AI-assisted workflow:** a wider system may combine model output with approved context or tools.
- **Analyst decision:** a human reviews the evidence, records the assessment, and follows the organisation's authorised process.

Knowing which component produced a result helps an analyst understand what was—and was not—checked.

## 3. Treat generated content as a draft

A well-written AI summary can still contain an incorrect identifier, an unsupported inference, or an important omission. A confident tone is not proof of accuracy.

A sound review should:

1. Open the original security record or event behind an important claim.
2. Confirm that the record supports the statement being made.
3. Separate directly observed facts from interpretation or hypothesis.
4. Preserve uncertainty when the available evidence does not support a firm conclusion.
5. Correct the case note before it is used for a decision or handover.

## 4. Apply three boundaries when using AI

### Data

Use only organisation-approved services. Share the minimum information needed and redact unnecessary sensitive details. Do not paste live alerts, internal hostnames, usernames, credentials, or confidential case context into public or unapproved AI services.

### Evidence

Treat generated indicators, identifiers, queries, citations, and conclusions as unverified until they have been checked against the native source.

### Authority

A model's recommendation is not permission to isolate a host, block an indicator, change an account, or close a case. Actions with operational impact must follow approved playbooks and authorisation requirements.

## 5. What this means for an L1 SOC analyst

AI may collect context or prepare an initial summary before an analyst starts reviewing an alert. The L1 analyst still needs the skills to:

- Understand the alert and its context.
- Review the original process, endpoint, and network records relevant to the investigation.
- Identify missing evidence and unsupported claims.
- Record a clear, evidence-based observation.
- Document open questions and pass useful context to the next tier.

Manual investigation skills remain important when an AI assistant is unavailable, its evidence references cannot be verified, or the supplied context is incomplete.

## 6. Practical learning reflection

In this room, I practised an alert investigation in Elastic Security and compared my review of the native event data with an AI-prepared case note. The exercise reinforced the importance of checking the source evidence rather than accepting a generated summary at face value. It also connected investigation quality with clear analyst-owned documentation and a useful handover to Tier 2.

## 7. Personal takeaway

My main takeaway is that AI should support the SOC analyst, not replace critical thinking. Effective use of AI depends on understanding its limits, protecting sensitive information, verifying important claims, and keeping response decisions within approved human-controlled processes.

---

**Scope:** These are high-level personal learning notes. They intentionally exclude room answers, flags, task solutions, exact queries, step-by-step instructions, and lab-specific identifiers.
