# CallProof

CallProof is an authority-aware operational verification app built for CALL-E. It turns a specific real-world assumption into one of three bounded outcomes: `VERIFIED`, `CONTRADICTED`, or `UNRESOLVED`.

The key design rule is simple: a completed phone call is not proof. CallProof only upgrades a claim when the answer is direct and the person answering is authoritative for that fact. Otherwise it abstains.

## Why this exists

Operational plans routinely depend on facts that are difficult to settle through web data or internal systems alone: access windows, site permissions, delivery constraints, approval status, dispatch readiness, and similar last-mile conditions.

CallProof uses CALL-E to ask the accountable real world, then separates:

- whether the claim itself was established,
- whether the recipient was authoritative,
- evidence quality,
- material qualifiers or conditions,
- and confidence in the returned evidence/result.

That means a result can correctly be **high-confidence `UNRESOLVED`** when the recipient clearly says they cannot authoritatively confirm the claim.

## Core loop

```text
CLAIM -> CALL-E -> WARRANT
```

1. State one operational claim precisely enough that the answer changes what happens next.
2. CALL-E contacts the nominated operational recipient and asks only what is needed to test the claim.
3. CallProof returns `VERIFIED`, `CONTRADICTED`, or `UNRESOLVED`, with authority, evidence quality, qualifiers, confidence and provenance.

## Example

Claim:

> A 7.5-tonne production vehicle may enter the site at 05:30 tomorrow.

A site contact says normal access starts at 06:00 and earlier access requires written duty-manager approval. Because the contact is authoritative and the answer directly rejects the 05:30 assumption, CallProof returns `CONTRADICTED` and preserves the approval condition as a material qualifier.

## Live CALL-E proof

The prototype includes a sanitized replay of a real CALL-E MCP run against the official hackathon test hotline.

The test claim was:

> The verification call successfully reached the test endpoint.

The call connected, but the hotline explicitly said it could identify itself only as a general inbound test hotline and could not confirm the stronger claim about a specific test endpoint. CALL-E completed the task with 0.90 (`high`) confidence.

CallProof therefore returns:

```text
UNRESOLVED
Result confidence: 90%
Authority: non_authoritative
Evidence: insufficient
```

This is intentional: confidence in the evidence/result is not the probability that the underlying claim is true.

## Safety and side effects

- The deterministic scenario and recorded proof place no calls.
- Live calling should only be used with numbers the operator is authorized to contact.
- A call connecting is never treated as evidence that the claim is true.
- Non-authoritative, ambiguous, failed or insufficient outcomes resolve to `UNRESOLVED`.
- CallProof does not make legal, medical, financial, emergency or identity decisions.
- Material qualifiers are surfaced rather than silently flattened into a yes/no result.

## Source

The full prototype, tests, live sanitized fixture and demo material live at:

https://github.com/adamblackoak/tool_shed/tree/main/callproof

The standalone repository contains a FastAPI UI, deterministic production-access scenario, sanitized live CALL-E fixture, verdict regression tests, and Developer API integration code.
