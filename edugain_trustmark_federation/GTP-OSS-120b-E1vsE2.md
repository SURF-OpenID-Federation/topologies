# Comparison of Scenario E1 (with Trust Marks) vs Scenario E2 (without Trust Marks)

## Legend
| Symbol | Meaning |
|--------|---------|
| 🟢 | **High** – the topology satisfies the dimension very well, even at large scale. |
| 🟡 | **Medium** – the topology is acceptable but shows noticeable trade‑offs or constraints. |
| 🔴 | **Low** – the topology presents a significant limitation for the dimension. |

---

## Evaluation Matrix

| Dimension | **Scenario E1** – Trust Mark enabled | **Scenario E2** – No Trust Marks |
|-----------|--------------------------------------|-----------------------------------|
| **Trust model** | 🟢 **H** – Hierarchical chain **plus** cryptographic Trust Mark proof gives a very expressive model (TA → IA → leaf) with explicit membership evidence. | 🟡 **M** – Hierarchical chain alone defines membership; no cryptographic proof beyond the chain, so the model is less expressive. |
| **Governance** | 🟢 **H** – Governance delegated to IAs; the TMO centralises the eduGAIN‑membership Trust Mark policy, giving clear authority over who may claim membership. | 🟡 **M** – Governance still delegated to IAs, but no centralised mechanism to enforce membership criteria; decisions rely on TA/IA policies only. |
| **Scalability** | 🟢 **H** – Load split (TA handles only IAs & TMO; each IA handles its own leafs and Trust Mark issuance). Adding more leaves does not affect the TA. | 🟢 **H** – Same hierarchical load distribution; scaling behaviour identical to E1 (no extra load from Trust Mark services). |
| **Availability** | 🟢 **H** – IA outage isolates its national subtree; the TA and other IAs stay up. Trust Mark services are local to each IA, so their failure only affects issuance of new marks, not existing ones. | 🟢 **H** – Same isolation; only the TA and the affected IA matter. No extra services to fail. |
| **Security** | 🟢 **H** – Dual‑condition (valid Trust Chain **and** valid eduGAIN Trust Mark) reduces blast radius; an attacker must compromise both the IA and obtain a trusted mark. | 🟡 **M** – Security relies solely on the Trust Chain. Compromise of an IA (or its keys) automatically grants eduGAIN membership to all its leafs. |
| **Policy** | 🟢 **H** – TA can enforce global policies; IAs can enforce national policies; TMIs can enforce Trust Mark‑specific policies (e.g., required profile). | 🟡 **M** – Policies can still be set at TA and IA levels, but there is no Trust Mark‑specific layer to express additional compliance requirements. |
| **Protocol support** | 🟢 **H** – Fully supports OIDC, OAuth, OpenID 4VCI/VP; Trust Mark verification is an extra step that is protocol‑agnostic. | 🟢 **H** – Same protocol support; no extra step required. |
| **Federation composition** | 🟢 **H** – IAs act as federations‑of‑federations; the TA can federate with other global anchors while preserving Trust Mark semantics. | 🟢 **H** – Same compositional structure; just without Trust Mark layer. |
| **Operations** | 🟡 **M** – Onboarding/off‑boarding requires (a) IA registration **and** (b) issuance of a Trust Mark by the appropriate TMI. Extra operational process for Trust Mark lifecycle. | 🟢 **H** – Simpler operations: only IA registration/off‑boarding; no Trust Mark issuance or revocation to manage. |
| **Auditability** | 🟢 **H** – Complete audit trail: Trust Chain + signed Trust Mark (incl. `iss`, `sub`, `iat`, `exp`). Easy to answer “Why did A trust B at time T?”. | 🟡 **M** – Audit trail limited to Trust Chain; no cryptographic evidence of compliance beyond the chain, making it harder to prove that a leaf satisfied eduGAIN‑specific criteria. |
| **Privacy** | 🟡 **M** – Trust Mark issuance introduces an extra identifier (`iss`) that reveals the issuing IA. However, this is required for verification and is limited to the national level. | 🟢 **H** – Fewer metadata elements exposed (no Trust Mark issuer), so the leaf’s affiliation is only visible through the IA hint. |
| **Performance** | 🟡 **M** – Validation needs (1) Trust Chain resolution **and** (2) Trust Mark signature verification. Slight additional CPU and network overhead. | 🟢 **H** – Only Trust Chain resolution; fewer cryptographic checks, lower latency. |
| **Resilience** | 🟢 **H** – Partial failures (IA, TMI, or TMO) affect only the corresponding national subtree; the rest of the federation continues. | 🟢 **H** – Same isolation; only IA failure impacts its subtree. |
| **Migration** | 🟡 **M** – Moving a leaf between IAs requires (a) updating `authority_hints` **and** (b) obtaining a new Trust Mark from the target IA’s TMI. Two coordinated steps. | 🟢 **H** – Migration only needs to change `authority_hints` (single step). |
| **Legal / regulatory** | 🟢 **H** – Clear liability split: TA (global policy), IAs (national policy), TMIs (Trust Mark issuance compliance). Helps satisfy multi‑jurisdictional regulations. | 🟡 **M** – Liability split only between TA and IAs; no explicit entity responsible for demonstrating compliance with eduGAIN‑specific rules. |
| **Implementation complexity** | 🟡 **M** – Requires implementing TA, IAs, TMIs, and the Trust Mark issuance/verification stack (signing, revocation, status endpoint). | 🟢 **H** – Only TA and IAs need to be implemented; no Trust Mark infrastructure. |
| **OpenID Federation Capabilities** | 🟢 **H** – Uses full set of capabilities: Subordinate Statements, metadata policies, Trust Mark inclusion, resolved metadata derivation. | 🟢 **H** – Uses core capabilities (Subordinate Statements, metadata policies) but without Trust Mark extensions. |
| **Key rotation** | 🟢 **H** – Keys rotate locally at each IA and each TMI; TA rotation only impacts IA statements. Compromise of one key does not affect the whole federation. | 🟢 **H** – Same hierarchical key rotation; only TA and IA keys exist, making rotation easier (fewer components). |

---

## Overall Assessment

| Scenario | Strengths | Weaknesses |
|----------|-----------|------------|
| **E1 (Trust Mark enabled)** | Very strong security (dual‑condition), fine‑grained policy enforcement, excellent auditability, clear legal split of responsibilities. | Higher operational and implementation complexity; extra performance cost; slight privacy impact from exposing the Trust Mark issuer. |
| **E2 (No Trust Marks)** | Simpler to operate and implement; lower latency; better privacy; easier migration. | Weaker security (no cryptographic membership proof), less auditability, no explicit compliance evidence, broader blast radius if an IA is compromised. |

### Recommendation

- **If strong assurance of eduGAIN membership and traceable compliance are required** (e.g., for research collaborations, regulated data exchange), **Scenario E1** is the preferred choice despite its added complexity.  
- **If the federation prioritises simplicity, performance, and privacy**, and can accept that membership is inferred only from the hierarchical chain, **Scenario E2** may be sufficient.  

Many federations adopt a **hybrid approach**: they run the basic **E2** hierarchy for the majority of participants and add **optional Trust Marks** (as in **E1**) for entities that need to prove a higher level of trust. This yields the best of both worlds—basic scalability with the ability to elevate selected participants to a higher trust tier.
