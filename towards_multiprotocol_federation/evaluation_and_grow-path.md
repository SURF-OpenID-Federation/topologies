# Evaluation of Federation Topologies  
*Model 1 → Model 2 → Model 3* compared to the dimensions in the supplied **evaluation matrix**.

## 1️⃣ Summary Table  

| Dimension | **Model 1** – SURF TA + OIDC IA + SRAM | **Model 2** – + eduGAIN TA, TMOs, all TMIs | **Model 3** – + Wallet IA, Issuers & Verifiers |
|-----------|----------------------------------------|--------------------------------------------|----------------------------------------------|
| **Trust model** | ✓ Simple hierarchical (single TA) | ✓ Two‑level TA hierarchy (eduGAIN TA → SURF TA) | ✓ Three‑level (two parallel federations) |
| **Governance** | ✗ All authority centralized in SURF TA | ✓ TMOs delegate trust‑mark governance | ✓ Wallet IA can enforce its own policies |
| **Scalability** | ✗ Limited; single IA must serve all OPs | ~ Load split between two TAs, still one OIDC IA | ✓ Two independent IAs distribute traffic; can handle ~200 OPs & 2 000 leaf RPs |
| **Availability** | ✗ Single point of failure (TA + IA) | ~ eduGAIN TA offers fallback for TMOs, OIDC IA still single point | ✓ Separate IA endpoints; failure of one does not cripple the other |
| **Security** | ✗ Compromise of SURF TA compromises entire federation | ~ Separate TA limits blast radius for trust‑marks, OIDC IA still central | ✓ Two independent trust chains; isolation between OIDC and Wallet reduces blast radius |
| **Key rotation** | ✗ All leaves depend on one TA key | ~ TMOs can rotate independently; OIDC IA key still global | ✓ Independent key rotation for OIDC IA, Wallet IA, and Issuer/Verifier keys |
| **Policy** | ✗ Coarse‑grained, only at TA level | ~ Trust‑mark level policies via TMOs | ✓ Wallet IA can publish its own policies; clear inheritance boundaries |
| **Protocol support** | ✓ Pure OIDC – straightforward | ~ No new protocols, but TMOs can be reused for other protocols | ✓ Wallet IA adds OpenID 4VCI/4VP support without duplicating OIDC structure |
| **Federation composition** | ✗ No nesting possible | ~ Two TAs enable minimal federation‑of‑federations | ✓ Full federation‑of‑federations (OIDC IA ↔ Wallet IA ↔ two TAs) |
| **Operations** | ✗ All onboarding/offboarding via one TA | ~ TMOs automate trust‑mark onboarding; OIDC IA still bottleneck | ✓ Parallel teams can manage OIDC and Wallet sides independently |
| **Auditability** | ✗ Single chain, limited provenance | ~ Trust‑mark provenance adds traceability | ✓ Two independent chains give complete “why‑trust” explanations for both domains |
| **Privacy** | ✗ All relationships exposed from one endpoint | ~ TMOs can restrict visibility of specific marks | ✓ Wallet IA can publish minimal verifier metadata, reducing unnecessary disclosure |
| **Performance** | ✗ All requests hit same resolver → potential bottleneck | ~ Second TA spreads DNS/HTTP load | ✓ Parallel resolution halves average latency for large leaf sets |
| **Resilience** | ✗ No fallback; total outage on failure | ~ eduGAIN TA provides fallback for trust‑mark validation only | ✓ Two independent resolution paths + caching increase tolerance to partial failures |
| **Migration** | ✗ Hard to re‑architect; any change impacts whole federation | ~ Adding a TA already done; moving entities between TAs now feasible | ✓ Entities can migrate between OIDC IA, Wallet IA, or even across TAs without breaking the other federation |
| **Legal / regulatory** | ✗ All liability rests on SURF TA | ~ Responsibility split between eduGAIN TA (TMOs) and SURF TA | ✓ Issuer/Verifier roles in Wallet IA can be assigned to organisations with distinct legal obligations |
| **Implementation complexity** | ✗ Very low code base, but limited features | ~ Adds a second TA and several TMOs → modest increase | ✓ Three distinct components increase overall complexity, but each stays focused on a single protocol domain |
| **OpenID Federation capabilities** | ✓ Full support for trust‑chain resolution, trust‑marks, resolver endpoint (single chain) | ~ Same capabilities, plus ability to publish separate trust‑marks from eduGAIN TA | ✓ All core capabilities present in both federations; Wallet IA also exposes credential‑type metadata for VC use cases |

## 2️⃣ Issuer‑Level Influences  

| Issuer / Trust Entity | **Strengthens** | **Weakens / Risks** |
|-----------------------|----------------|---------------------|
| **SRAM (Trust‑Mark Issuer)** | Baseline “learning‑standards” mark; simple to manage | Single point of compromise; low policy granularity |
| **eduGAIN TM (Trust‑Mark Issuer)** | Widely recognised academic membership; supports cross‑border collaborations | Dependency on external service; adds metadata overhead |
| **REFEDS TM** | Enables research‑specific policies | Same dependency risk as eduGAIN TM |
| **Learning Standards Authority (LSA)** | Domain‑specific compliance mark; useful for credential issuance | Additional issuer to manage; frequent key rotation can increase coordination load |
| **eduGAIN TA** (top‑level) | Delegated governance via TMOs; reduces blast radius of SURF TA | Still a single point of failure for all trust‑mark validation |
| **SURFconext TA** | Core anchor for OIDC federation; trusted by all existing OPs/RPs | Centralized authority → high impact if compromised |
| **Intermediate – OIDC IA** | Isolates OIDC‑specific chain; limits scope of OIDC failures | Single intermediate for all OIDC entities → limited horizontal scaling |
| **Intermediate – Wallet IA** | Parallel federation for VC use cases; isolates credential flow | Extra operational overhead; need separate monitoring & key management |
| **Intermediate – SURF IA / Nikhef IA** (sub‑intermediates) | Delegates OP/RP leafs, reduces load on top‑level OIDC IA; enables local key‑rotation policies | Increases chain depth (now 3 hops) → added latency if not cached |
| **Issuers (SURF, Nikhef, Univ A/B, eduID)** | Scoped to credential issuance; limits blast radius of key compromise | Separate onboarding & policy management; potential for policy conflicts with OIDC side |
| **Verifiers (SURF‑Dept‑1, Exam System)** | Concrete verification endpoints; can enforce credential‑type policies | Risk of metadata over‑exposure; must keep trust‑chains up‑to‑date |

### Key Take‑aways  

1. **Model 2** provides the biggest governance upgrade by introducing a second TA and TMOs, which improves **policy granularity**, **auditability**, and **legal liability distribution** while keeping operational complexity moderate.  

2. **Model 3** adds a fully independent **Wallet IA**, giving the federation **horizontal scalability**, **protocol diversification** (OpenID 4VCI/4VP), and **better resilience** (two independent resolution paths). The trade‑off is higher implementation and operational complexity.  

3. The two **sub‑intermediates** (SURF IA & Nikhef IA) are valuable for **delegation** and **local key rotation**, but they increase the trust‑chain depth; caching is essential to keep latency low.  

4. A diverse set of **trust‑mark issuers** enriches policy expression but also increases the amount of metadata that must be fetched and validated. For a landscape of ~200 OPs/Issuers and ~2 000 leaf RPs, ensure resolvers support efficient bulk retrieval and caching.  

## 3️⃣ Recommended Growth Path  

| Step | Action | Matrix Dimensions Addressed |
|------|--------|-----------------------------|
| **1 → 2** | Deploy **eduGAIN TA**, register TMOs (**eduGAIN Membership** & **REFEDS**), add the four trust‑mark issuers (eduGAIN, REFEDS, SRAM, LSA). | Governance, Policy, Auditability, Legal/Regulatory, Federation Composition |
| **2 → 3** | Spin up **Wallet IA** with its own resolver endpoint; onboard Issuer & Verifier leafs. Keep the existing OIDC IA unchanged. | Scalability, Performance, Resilience, Protocol Support, OpenID Federation Capabilities |
| **Ongoing** | Implement **caching** at TA and IA layers; schedule **independent key‑rotation** for each IA and each issuer/verifier set. | Availability, Key Rotation, Performance, Security |
| **Future** | Consider a **federation‑of‑federations** (e.g., national “Higher‑Ed” federation that federates eduGAIN TA with a partner country’s TA). | Federation Composition, Migration, Legal/Regulatory |

By following this staged approach you can **grow safely**, maintain a clear audit trail, distribute legal responsibility, and keep performance within acceptable bounds while supporting the target scale of ~200 OPs/Issuers and ~2 000 leaf RPs.  

---  

*Feel free to ask for deeper dives into any specific dimension, or for a concrete implementation plan for caching/key‑rotation.*
