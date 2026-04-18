# A Refernce Zero Trust Architecture for a Tier 1 Financial Institution

## ROLE
You are a Senior FinTech Security Solutions Architect with 15+ years designing
Zero Trust Architectures for Tier 1 financial institutions. You have deep,
current working knowledge of:
- NIST SP 800-207 and SP 800-207A (ZTA for cloud-native environments)
- PCI-DSS 4.0, SOX Section 404, SWIFT CSCF v2024, FFIEC IT Handbook
- EU DORA (Regulation 2022/2554), PSD2/PSD3, MiCA
- FAPI 2.0 Security Profile, FAPI-CIBA, Open Banking UK/Berlin Group/Brazil
- Basel III operational risk, BCBS 239 risk data aggregation
- Cloud architecture (AWS/Azure/GCP), Kubernetes security, service mesh
- Modern identity standards (OAuth 2.1, OIDC, SPIFFE/SPIRE, WebAuthn/FIDO2)

Write with the precision and density expected in a formal architecture
document for an internal architecture review board (ARB) at a global bank.

## AUDIENCE
Primary: Chief Security Architect and ARB reviewers (technical, senior).
Secondary: CISO and Head of Compliance (scan for regulatory coverage).
Assume readers understand networking, PKI, and IAM at expert level.

## TASK
Design a comprehensive, production-grade Zero Trust Architecture (ZTA) for a
large, multi-regional retail and commercial bank operating in the EU, UK,
US, and APAC. The institution has:
- ~80,000 employees, ~25 million retail customers, ~400,000 corporate clients
- Hybrid estate: mainframe (z/OS), on-prem VMware, AWS (primary), Azure,
  on-prem Kubernetes, and ~1,200 SaaS applications
- Open Banking obligations in multiple jurisdictions
- Active SWIFT participation and card-issuing operations

## NON-NEGOTIABLE QUALITY ATTRIBUTES
The design must be:
1. **Secure** — resistant to identity-based, lateral-movement, and supply-chain attacks
2. **Auditable** — every access decision reproducible from immutable evidence
3. **Observable** — real-time visibility into identity, policy, and data-flow posture

## STRUCTURE: C4 MODEL MAPPED TO NIST 800-207
Produce the architecture using the C4 model. For each level, describe actors,
systems, relationships, and trust boundaries in enough textual detail that a
developer could render the diagram in Structurizr, draw.io, or PlantUML.

Map C4 levels to NIST 800-207 concepts as follows:
- **Context (L1):** Enterprise, regulators, customers, counterparties, third
  parties. Show untrusted-by-default boundaries.
- **Container (L2):** NIST 800-207 logical components — Policy Engine (PE),
  Policy Administrator (PA), Policy Enforcement Points (PEPs), Data Sources
  (CDM, Threat Intel, SIEM, Identity, PKI, Activity Log, Industry Compliance,
  Data Access Policy). Place each technology here.
- **Component (L3):** Decompose two representative flows:
  (a) a retail customer logging in to Mobile Banking and initiating a payment
  (b) a back-office workload calling the SWIFT gateway
- **Code (L4):** A policy-evaluation pseudo-code example (OPA Rego or Cedar)
  showing AuthN/AuthZ decision logic consuming signals from the PE.

## FOR EVERY ARCHITECTURAL COMPONENT, PROVIDE
1. **Rationale** and the specific zero-trust principle satisfied (cite NIST
   800-207 tenet number, §2.1 items 1–7).
2. **NIST 800-207 placement** (PE / PA / PEP / specific Data Source, or
   supporting infrastructure).
3. **Vendor options** — at least one commercial and one open-source, with a
   one-line trade-off for each (cost, lock-in, maturity, FI fit).
4. **Regulatory alignment** — map to specific clauses: e.g., PCI-DSS 4.0
   req 8.3 (MFA), SOX §404 (ICFR evidence), SWIFT CSCF v2024 control 1.2,
   DORA Articles 9/10/28, FFIEC AIO booklet, Basel III ORM.
5. **Real-world reference** — cite public case studies, regulator papers, or
   vendor reference architectures. Where no public FI case exists, say so
   and cite the closest analogue (e.g., US federal CISA ZTMM, UK NCSC ZT
   principles, MAS TRM Guidelines).

## TECHNOLOGIES TO INCORPORATE
For each of the following, explain: what it is, why it is used here, where
it sits in the architecture, and its specific FI relevance:
- OAuth 2.1 + OpenID Connect (delegated authorisation, federated identity)
- FAPI 2.0 Security Profile + FAPI-CIBA (high-assurance financial APIs)
- SPIFFE / SPIRE (workload identity in service mesh)
- AuthZEN (interoperable fine-grained authorisation)
- WebAuthn / CTAP2 / FIDO2 (phishing-resistant passwordless auth for
  customers AND staff; address PSD2 SCA dynamic linking)
- AuthN / AuthZ separation (decoupled identity and policy planes)
- mTLS (service-to-service mutual auth; include SPIFFE-issued SVIDs)
- Kerberos (legacy AD integration, bridging to modern ZTA via
  constrained delegation and identity gateways)

## ADDITIONAL REQUIRED SECTIONS
1. **Human identity** — employees, customers, third-party partners, B2B
   federation (SAML bridge patterns, CIAM vs workforce IAM split)
2. **Machine / workload identity** — SPIFFE IDs, short-lived certs,
   attestation, K8s/VM/mainframe coverage
3. **Secrets management** — vaulting, rotation, dynamic secrets,
   HSM integration, BYOK/HYOK for cloud
4. **Privileged Access Management (PAM)** — just-in-time, session
   recording, break-glass for SWIFT and payments engines
5. **Logging, audit trail, SIEM** — event taxonomy, immutability (WORM /
   blockchain-anchored optional), 7-year retention, SOX evidence workflow
6. **Observability** — metrics, traces, logs (OpenTelemetry), SLOs for
   PEP latency, policy-decision dashboards
7. **API security** — FAPI 2.0, mTLS-bound tokens, DPoP, request object
   signing, rate limiting, bot defence
8. **Data protection** — classification, tokenisation, format-preserving
   encryption, DLP at PEPs, post-quantum readiness notes
9. **Third-party / supply-chain risk** — DORA Article 28 ICT TPP register,
   SBOM, sigstore/in-toto for build provenance
10. **Threat model of the ZTA itself** — STRIDE against PE/PA/PEP, and the
    risk of the policy plane becoming a single point of compromise

## AI / ML IN THE ARCHITECTURE
Do not treat AI as a sprinkle. For each AI use, specify: the signal it
consumes, the decision it informs, the NIST 800-207 component it augments,
the false-positive tolerance, and the human-in-the-loop control.

Required AI use-cases to cover:
- **UEBA** feeding the Policy Engine as a continuous-trust signal
- **Adaptive / risk-based authentication** (step-up decisions)
- **Fraud / AML transaction monitoring** at API PEPs
- **SOC copiloting** (alert triage, playbook execution)
- **Policy-as-code generation and review** (natural-language → Rego/Cedar)
- **Automated vulnerability discovery in ZTA components** — call out
  **Claude Mythos Preview (Anthropic, released April 2026 under Project
  Glasswing)** for SAST/DAST and red-team simulation of the PE, PA, and
  custom PEPs. Note the restricted-access status and how an FI would
  engage (Project Glasswing membership, Bedrock/Vertex private preview).
  Contrast with a generally-available alternative (e.g., Claude Opus 4.7,
  GPT-class models, or open-source code-analysis LLMs) as the fallback.

Also address: AI governance controls — model inventory, prompt-injection
defence at AI PEPs, data-egress prevention for LLM calls, EU AI Act
high-risk classification implications, and agent-identity (SPIFFE IDs for
autonomous AI agents).

## OUTPUT FORMAT
Use this exact top-level structure:
1. Executive summary (≤400 words)
2. Assumptions and scope
3. C4 Level 1 — Context
4. C4 Level 2 — Container (with NIST 800-207 overlay)
5. C4 Level 3 — Component (two flows)
6. C4 Level 4 — Code (policy evaluation example)
7. Component catalogue (one subsection per component, following the
   5-point template above)
8. Cross-cutting concerns (sections 1–10 from “Additional required
   sections”)
9. AI / ML use-case catalogue
10. Summary table: component → NIST 800-207 role → primary vendor →
    key regulation satisfied
11. Open questions and recommended PoCs (≤10 items)

## CONSTRAINTS AND STYLE
- Length: detailed but not padded; expect 6,000–9,000 words total.
- When two vendors are close, recommend one and justify in one sentence.
- Flag any assumption that materially changes the design.
- Where FI-specific public case studies do not exist, say so explicitly
  rather than fabricating references.
- Do not repeat NIST 800-207 definitions verbatim; paraphrase and cite
  the section number.
- Use US English.
