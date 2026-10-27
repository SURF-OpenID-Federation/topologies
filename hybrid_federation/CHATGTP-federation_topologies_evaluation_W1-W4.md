# Evaluation of Federation Topologies H1–H4

## 1. Executive summary

This document evaluates all four federation topologies (H1, H2, H3, and H4) against the 18 criteria in the supplied evaluation matrix. The assessment is qualitative and does not use a quantitative scoring matrix.

The designs assume a federation based on the [OpenID Federation 1.0 specification](https://openid.net/specs/openid-federation-1_0.html) and the [OpenID Federation for Wallet Architectures 1.0 specification](https://openid.net/specs/openid-federation-wallet-1_0.html).

The central architectural distinction is where authority is held, how entities are grouped, and how much responsibility is delegated from the Trust Anchor to Intermediate entities.

- **H1 — Flat federation:** The Trust Anchor directly resolves metadata for most participating entities. Univ B and eduID are represented as combined OP/Issuer entities.
- **H2 — Organizational intermediates:** The Trust Anchor resolves top-level entities, while organizational Intermediates represent selected subordinate RPs and Verifiers.
- **H3 — Organizational intermediates with dual-role grouping:** Similar to H2, but Univ B and eduID each have an Intermediate that groups their OP and Issuer entities.
- **H4 — Protocol-specific intermediates:** SURFconext resolves two domain-specific Intermediates: one for all OIDC OPs/RPs and one for all wallet Issuers/Verifiers. This separates trust administration by protocol domain rather than by organization.

### Overall assessment

| Topology | Main strength | Main weakness | Best fit |
|---|---|---|---|
| **H1: Flat** | Simple hierarchy and direct visibility of entities | Centralized management and limited organizational delegation | Small or relatively stable federations with centralized governance |
| **H2: Organizational intermediates** | Organizational delegation with manageable hierarchy | Delegation is not applied consistently to all roles and organizations | Federations organized around institutional boundaries |
| **H3: Dual-role organizational intermediates** | Explicitly groups OP and Issuer entities for selected organizations | More entities and trust relationships to manage; grouping needs a clear purpose | Federations where selected organizations need a shared trust boundary across identity and wallet roles |
| **H4: Protocol-specific intermediates** | Clear separation between OIDC and wallet trust administration; Trust Anchor has only two direct Intermediate relationships | Each domain Intermediate becomes a broad authority; organizational accountability is less explicit and a failure or compromise can affect many entities in that domain | Federations that want separate policy and operational control for OIDC and wallet participants |

**Initial recommendation:** H2 is a strong baseline if organizational autonomy is important. H3 is worth considering when organizations acting as both OPs and Issuers need an explicit shared organizational trust boundary. H4 is attractive when policy, operations, or security requirements differ by protocol domain, but its two broad Intermediates should have carefully constrained authority and clear operational ownership. H1 remains viable where simplicity and centralized governance outweigh the benefits of delegation.

These are architectural recommendations, not formal compliance findings. A Mermaid diagram describes an intended architecture, but does not by itself prove conformance with OpenID Federation. In particular, an arrow indicating that an Intermediate groups an entity does not automatically establish a valid federation relationship or a valid Trust Chain.

---

## 2. Evaluation approach and standards interpretation

The assessment distinguishes three kinds of properties:

1. **Architectural properties:** What follows from the proposed structure, such as hierarchy depth, organizational delegation, and the number of direct relationships.
2. **Protocol capabilities:** What OpenID Federation and the wallet architecture specification provide, including signed Entity Statements, Trust Chains, metadata policies, Trust Marks, and resolution.
3. **Implementation-dependent properties:** Outcomes that depend on deployment choices, such as high availability, caching, key management, operational procedures, and auditing.

### 2.1 Important protocol considerations

#### A. Trust Anchor membership is not the same as metadata resolution

OpenID Federation represents hierarchical trust through signed Entity Configurations and Subordinate Statements. An entity's `authority_hints` identify potential superior authorities. A diagram arrow labelled `fetch/resolve` should be read as an operational relationship, not automatically as federation membership. Actual trust requires appropriate Entity Statements and a valid Trust Chain.

#### B. Intermediate entities are trust authorities, not merely organizational containers

An Intermediate can issue Subordinate Statements about its immediate subordinates, apply metadata policies, and delegate responsibility within the federation. An organizational grouping is meaningful as a trust boundary only if the corresponding federation relationships, signing keys, statements, and policy rules are implemented correctly.

#### C. Multiple trust relationships are possible, but are not shown

OpenID Federation can support trust evaluation against configured Trust Anchors and may allow more than one possible Trust Chain. The H1–H4 diagrams primarily depict a single Trust Anchor and hierarchy. They do not explicitly model multiple Trust Anchors, cross-federation trust, or alternative chains. Those should be treated as possible extensions rather than existing properties of the diagrams.

#### D. Trust Marks and federation membership are separate concepts

A Trust Mark is a signed assertion about an entity, such as accreditation or compliance with a trust framework. Federation membership does not itself prove that an entity satisfies every policy or accreditation requirement. The Trust Anchor must recognize the relevant Trust Mark issuers, and Trust Marks must be validated according to the applicable rules.

The arrows between SURFconext and eduGAIN, REFEDS, and SRAM do not fully represent Trust Mark issuance, validation, applicability, or revocation.

#### E. Wallet roles require appropriate metadata and trust evaluation

Representing an entity as an Issuer or Verifier does not by itself establish that it can participate securely in a wallet ecosystem. The relevant wallet-specific metadata, keys, policies, and trust requirements must also be defined and validated. A Verifier must establish trust in a Credential Issuer through an acceptable Trust Chain and apply the relevant issuance-entitlement requirements.

---

## 3. Comparative evaluation across the 18 dimensions

### Rating scale

| Rating | Interpretation |
|---|---|
| **Very strong** | The architecture provides a clear structural advantage for this dimension. |
| **Strong** | The architecture supports the requirement well, subject to implementation. |
| **Moderate** | The architecture is viable but has meaningful trade-offs or limitations. |
| **Weak** | The architecture introduces a substantial disadvantage or does not explicitly address the requirement. |
| **Conditional** | The result depends heavily on implementation choices or additional architectural decisions. |

These are comparative judgments, not formal compliance scores.

| Dimension | H1: Flat | H2: Organizational intermediates | H3: Dual-role organizational intermediates | H4: Protocol-specific intermediates |
|---|---|---|---|---|
| 1. Trust model | Moderate | Strong | Strong | Strong, if domain-wide authority is intended |
| 2. Governance | Weak–Moderate | Strong | Strong | Strong by protocol domain; weaker by organization |
| 3. Scalability | Moderate | Strong | Strong | Strong for adding entities within either domain |
| 4. Availability | Moderate | Moderate–Strong | Moderate–Strong | Conditional; each domain Intermediate is a broad dependency |
| 5. Security | Moderate | Strong | Strong | Conditional; useful separation, but large domain-wide blast radius |
| 6. Key rotation | Moderate | Strong | Strong | Moderate–Strong; two central Intermediate keys need robust procedures |
| 7. Policy | Moderate | Strong | Strong | Strong for protocol-specific policies |
| 8. Protocol support | Moderate | Strong | Strong | Strong separation of OIDC and wallet roles |
| 9. Federation composition | Moderate | Strong | Strong | Moderate; domain split is clear, external composition is not shown |
| 10. Operations | Strong initially | Strong | Moderate–Strong | Moderate–Strong; simpler top-level structure, broad Intermediate duties |
| 11. Auditability | Strong for direct relationships | Strong | Strong | Strong by domain, but less explicit organizational accountability |
| 12. Privacy | Moderate | Strong | Strong | Conditional; domain boundaries do not themselves limit metadata disclosure |
| 13. Performance | Strong for direct trust chains | Moderate–Strong | Moderate–Strong | Moderate; one additional hierarchy level for all leaves |
| 14. Resilience | Moderate | Moderate–Strong | Moderate–Strong | Conditional; two critical domain authorities |
| 15. Migration | Moderate | Strong | Strong | Moderate; moving entities between domains or authorities requires coordinated changes |
| 16. Legal / regulatory | Moderate | Strong | Strong | Moderate–Strong if domain responsibilities align with legal accountability |
| 17. Implementation complexity | Strong initially | Moderate | Moderate–Weak | Moderate; fewer top-level links but two broad policy/operational domains |
| 18. OpenID Federation capabilities | Strong for basic federation | Strong | Strong | Strong in principle; conformance details remain unspecified |

**Interpretation:** H1 benefits from a simpler hierarchy and fewer Intermediate-level dependencies. H2 and H3 provide more organizational delegation. No topology automatically delivers high availability, strong security, or regulatory compliance; those properties depend on deployment and governance.

---

## 4. Detailed evaluation by dimension

### 4.1 Trust model

**Evaluation question:** How is trust established, how is authority delegated, and can entities participate in multiple trust domains?

#### H1 — Flat federation

The Trust Anchor is the central authority for the participating entities. Each entity is shown as directly connected to SURFconext, including OPs, RPs, and Verifiers.

**Advantages**
- Trust relationships are relatively straightforward to understand.
- Direct membership simplifies the conceptual model.
- There are fewer Intermediate-level delegations to validate.

**Limitations**
- Organizational trust boundaries are not explicitly represented.
- Every entity depends on the Trust Anchor's acceptance and applicable policies.
- The diagram does not show multiple Trust Anchors or alternative Trust Chains.

The combined OP/Issuer representation for Univ B and eduID shows that an organization may perform multiple roles, but does not establish how those roles should be governed independently.

**Assessment: Moderate.** Suitable for a centrally governed federation, but less expressive when organizational autonomy or multiple trust domains are important.

#### H2 — Organizational intermediates

SURFconext directly recognizes two organizational Intermediates: SURF and Nikhef. These represent the organizational boundary for selected subordinate RPs and Verifiers.

**Advantages**
- Organizational trust delegation is explicitly represented.
- SURF and Nikhef can potentially manage their subordinate entities under delegated authority.
- The Trust Anchor need not directly govern every subordinate entity.

**Limitations**
- Univ A, Univ B, and eduID do not receive the same organizational delegation in the diagram.
- The trust model is only partially decentralized.
- The direct membership of OPs and Issuers alongside organizational Intermediates creates an asymmetric structure.

**Assessment: Strong.** It introduces organizational delegation while preserving centralized control of top-level trust.

#### H3 — Organizational intermediates with dual-role grouping

H3 extends H2 by introducing `IA_B` and `IA_EDUID`. Each represents an organization that provides both OP and Issuer services.

**Advantages**
- Multiple protocol roles can be associated with an organizational trust boundary.
- An organization can potentially coordinate onboarding, policies, and key-management procedures across those roles.
- The architecture can represent organizations that provide identity and credential services.

**Limitations**
- The Intermediates create additional trust authorities and signing-key dependencies.
- The diagram does not establish whether OP and Issuer services must share policies, keys, administration, or infrastructure.
- Univ B and eduID are grouped, while other organizations are represented differently. This asymmetry needs a governance rationale.

**Assessment: Strong.** More expressive than H2, but the additional Intermediates are justified only if they provide meaningful delegation or policy enforcement.

**Conclusion:** H1 is the simplest. H2 and H3 align more naturally with organizational delegation. H3 is most useful where dual-role organizations need a shared organizational trust boundary.

#### H4 — Protocol-specific intermediates

H4 makes protocol domain, rather than institutional ownership, the main delegation boundary. `IA_OIDC` has all five OPs and all four RPs as subordinates; `IA_Wallet` has all five Issuers and both Verifiers. This gives each domain a consistent hierarchy and can support different metadata policies or operational processes for OIDC and wallet services.

The trade-off is that each Intermediate has a broad subordinate set spanning multiple organizations. The diagram does not show an organizational Intermediate for SURF, Nikhef, Univ A, Univ B, or eduID, and an organization's OP and Issuer sit under different authorities. H4 therefore does not create a shared organizational trust boundary for an institution's identity and wallet services.

**Assessment: Strong if protocol-domain delegation is the intended trust model; otherwise potentially misaligned with organizational governance.**

### 4.2 Governance

**Evaluation question:** Who can onboard entities, change policies, delegate authority, and offboard participants?

| Topology | Governance implications | Main concern |
|---|---|---|
| H1 | SURFconext retains direct oversight of nearly every entity. | Central administrative workload and limited delegation. |
| H2 | SURF and Nikhef can potentially administer subordinate entities. | Delegation is uneven across organizations and roles. |
| H3 | Selected organizations have explicit Intermediates for combined OP/Issuer roles. | Responsibility and policy ownership must be defined for each Intermediate. |

H1 is the most centralized design. This can be advantageous when one operator is responsible for all membership decisions and operational policies.

H2 distributes some governance to organizational Intermediates. SURFconext remains the federation-level authority, while organizational Intermediates can manage subordinate entities within delegated limits.

H3 extends that approach to organizations operating both identity and credential services. It is useful when Univ B and eduID need to manage those services under a common organizational authority.

**Important qualification:** A separate Intermediate does not automatically create organizational autonomy. Its actual authority depends on delegation, Subordinate Statements, policy constraints, and administrative procedures.

**Recommendation:** H2 for general institutional governance; H3 when dual-role organizations need a shared organizational trust boundary.

#### H4 — Protocol-specific intermediates

H4 creates two administrative domains: OIDC federation and wallet federation. This can clarify who owns protocol-specific onboarding, metadata rules, and service operations. However, `IA_OIDC` and `IA_Wallet` each span multiple organizations, so the diagram does not establish how SURFconext delegates authority to institutions or how each institution approves changes to its own entities.

**Governance decision needed:** Are these Intermediates centrally operated protocol authorities, or are they intended to delegate operational control to member organizations? If organizations need local autonomy, H4 may need organizational sub-intermediates beneath the protocol-domain layer, or a different structure.

### 4.3 Scalability

**Evaluation question:** How does complexity grow as the federation adds entities, organizations, and trust relationships?

Let \(N\) be the number of leaf entities and \(I\) the number of organizational Intermediates.

| Topology | Approximate hierarchy depth for a leaf | Main scaling characteristic |
|---|---|---|
| H1 | 1 edge from leaf to Trust Anchor | The Trust Anchor manages a growing number of direct relationships. |
| H2 | Up to 2 edges from leaf through Intermediate to Trust Anchor | Delegation reduces direct membership management by the Trust Anchor. |
| H3 | Up to 2 edges for grouped entities; 1 for direct members | More delegation boundaries, but more Intermediate entities to manage. |

These depths describe the intended hierarchy, not a guarantee about the number of statements, HTTP requests, or resolution operations.

- **H1:** The Trust Anchor's direct entity-management workload grows with the number of participating entities. The design is easy to understand but becomes less convenient when many organizations need independent administrative control.
- **H2:** Adding a subordinate entity to an existing Intermediate can avoid adding another direct organizational relationship at the Trust Anchor level. This supports horizontal growth, particularly when organizations have many subordinate entities.
- **H3:** H3 has the same basic scaling advantage as H2, with additional Intermediates for dual-role organizations. This adds fixed operational complexity but may reduce the need to manage each role independently at the top level.

**Assessment:** H2 and H3 provide better structural support for a large federation than H1. H2 has less fixed complexity than H3; H3 may be worthwhile where shared organizational authority is important.

Real-world scalability also depends on Trust Chain depth, statement expiration, metadata size, caching, resolver design, and request volume.

#### H4 — Protocol-specific intermediates

H4 reduces the Trust Anchor's direct relationships to two Intermediates regardless of the number of leaf entities. Adding an OP or RP would ordinarily add it beneath `IA_OIDC`; adding an Issuer or Verifier would add it beneath `IA_Wallet`. This is structurally scalable for a federation whose policies and ownership are organized by protocol domain.

The cost is concentration: each Intermediate must manage statements, policy, keys, monitoring, and lifecycle processes for a large and potentially heterogeneous set of entities. If per-organization autonomy is required, the two-level split may not be sufficient.

**Assessment:** Strong for scaling by protocol domain; less suitable if growth requires institution-specific delegated administration.

### 4.4 Availability

**Evaluation question:** What happens if the Trust Anchor, an Intermediate, or a federation endpoint becomes unavailable?

| Topology | Principal dependency | Potential mitigation |
|---|---|---|
| H1 | Trust Anchor and direct entity endpoints | Cache valid Entity Statements and resolved metadata. |
| H2 | Trust Anchor, organizational Intermediates, and leaf endpoints | Cache statements and resolver results; operate Intermediates redundantly. |
| H3 | Same as H2, with additional dual-role Intermediates | Independent infrastructure and key-management processes for each Intermediate. |

In H1, a failure of the Trust Anchor's infrastructure can affect trust decisions throughout the federation, particularly when fresh statements or resolution results are required.

In H2 and H3, an Intermediate failure can affect its subordinates if required statements cannot be retrieved and no sufficiently fresh statements or valid cached Trust Chains are available.

An entity endpoint being unavailable does not necessarily mean all existing trust relationships immediately become invalid. Previously retrieved, correctly validated statements may sometimes be used until they expire, provided the relying party's security policy permits it and no relevant revocation or policy change requires fresher information.

**Assessment:** H1 has fewer Intermediate dependencies; H2 and H3 introduce additional dependency points. All three require deliberate availability engineering.

#### H4 — Protocol-specific intermediates

H4 has two significant Intermediate dependencies. An outage or key problem at `IA_OIDC` can affect trust evaluation for all OIDC OPs and RPs; a corresponding problem at `IA_Wallet` can affect all Issuers and Verifiers. This is a wider domain-level dependency than an Intermediate serving only one organization.

Mitigations include redundant endpoints, protected signing keys, short and well-managed deployment windows, caching within permitted validity periods, monitoring, and tested recovery procedures. The two domains should not share a single failure mode merely because they share a Trust Anchor.

**Assessment:** Conditional. H4 is operationally manageable, but availability engineering for both domain Intermediates is critical.

### 4.5 Security

**Evaluation question:** What is the attack surface, and how far can the consequences of a compromised key or authority spread?

| Topology | Trust Anchor compromise | Intermediate compromise |
|---|---|---|
| H1 | Potentially affects the entire federation. | No organizational Intermediate is shown. |
| H2 | Potentially affects the entire federation. | Can affect entities subordinate to the compromised Intermediate. |
| H3 | Potentially affects the entire federation. | Can affect entities subordinate to the associated organizational Intermediate. |

- **H1:** The main advantage is a smaller number of authority layers and fewer Intermediate signing keys. The disadvantage is concentration of authority in the Trust Anchor.
- **H2:** An organizational Intermediate can potentially issue fraudulent Subordinate Statements for entities within its delegated scope if its signing authority is compromised, subject to validation rules and constraints.
- **H3:** H3 can isolate organizational delegation for dual-role entities. However, a compromised dual-role Intermediate may affect both identity and credential services associated with that organization.

A smaller blast radius is possible only if the Intermediate's authority is constrained and the federation enforces those boundaries. Shared administrative credentials or signing infrastructure can undermine the intended isolation.

**Recommendation:** H2 offers a clear opportunity to limit delegated authority. H3 can do so at the organizational level. H1 is simpler but concentrates more trust-management responsibility at the top.

Actual security depends on key protection, policy constraints, endpoint security, monitoring, revocation, and recovery procedures.

#### H4 — Protocol-specific intermediates

H4 separates authority by protocol, which can limit direct policy mixing between OIDC and wallet entities. It can also allow distinct keys, administrators, and controls for the two domains. But a compromised `IA_OIDC` potentially affects every subordinate OIDC entity, and a compromised `IA_Wallet` potentially affects every subordinate wallet entity. Because each spans several organizations, the blast radius is broad.

The design should define strict delegation scope, metadata-policy constraints, independent key custody, incident escalation, and a mechanism for promptly replacing or distrusting a compromised Intermediate. The diagram does not show whether either Intermediate can create further subordinates or whether its authority is constrained.

**Assessment:** Potentially strong separation between protocol domains, but broad Intermediate authority must be treated as a major security boundary.

### 4.6 Key rotation

**Evaluation question:** How much operational work is required when an entity or federation authority rotates its signing keys?

| Topology | Expected impact |
|---|---|
| H1 | Leaf rotation is relatively straightforward, but Trust Anchor rotation has federation-wide implications. |
| H2 | Leaf rotation can be managed locally; Intermediate rotation affects its delegated subtree. |
| H3 | Similar to H2, with additional Intermediate keys for dual-role organizations. |

Leaf key rotation is normally less disruptive than Trust Anchor key rotation, provided updated key material is published and validated correctly.

For H2 and H3, an Intermediate's rotation can affect validation of statements it issues to its subordinates. The Trust Anchor's rotation is particularly important because its keys establish the federation's root of trust.

**Assessment:** H2 balances delegated key management and operational complexity. H3 can clarify ownership of rotation but introduces more authority keys to manage. None is inherently safer without a well-defined rollover procedure.

#### H4 — Protocol-specific intermediates

H4 centralizes subordinate-statement signing for each protocol domain in two Intermediate authorities. Leaf key rotations can remain local to individual entities, but rotation of either Intermediate's keys affects the trust chains of all its subordinates. The two domain keys are therefore high-value operational assets.

Define planned rollover, overlap of old and new keys, publication timing, rollback, emergency compromise response, and monitoring. Keep the OIDC and wallet Intermediate key-management processes independently recoverable.

**Assessment:** Moderate–Strong if key operations are mature; the small number of authority keys simplifies inventory but increases the impact of each authority-key incident.

### 4.7 Policy

**Evaluation question:** How can security policies be expressed, inherited, constrained, and combined?

OpenID Federation provides two relevant mechanisms:

- **Metadata policies:** Rules applied to metadata through Subordinate Statements, allowing higher-level authorities to constrain or transform metadata.
- **Trust Marks:** Signed assertions that an entity satisfies a particular trust-framework requirement.

These mechanisms complement one another. Metadata policies govern technical metadata, while Trust Marks can provide evidence of accreditation or compliance.

| Topology | Policy advantage | Policy limitation |
|---|---|---|
| H1 | Policies can be applied centrally by the Trust Anchor. | Limited structural support for differentiated organizational policy administration. |
| H2 | Policies can be applied at federation and organizational levels. | Organizational policy differences need explicit rules and constraints. |
| H3 | Organizational policies can potentially cover both OP and Issuer services. | Different protocol roles may still require different policies. |

H1 suits a uniform policy model. H2 supports federation-wide requirements combined with organizational rules. H3 can support shared organizational requirements across roles, while retaining role-specific requirements.

**Recommendation:** H2 for hierarchical policy delegation; H3 for organizational consistency across roles.

The diagrams do not specify actual policies, Trust Mark types, trusted issuers, or conflict-resolution rules. These are necessary for a definitive policy assessment.

#### H4 — Protocol-specific intermediates

H4 is well suited to policies that differ by protocol domain: OIDC metadata requirements can be applied through `IA_OIDC`, while wallet Issuer/Verifier requirements can be applied through `IA_Wallet`. That may be valuable because credential issuance and verification often have requirements that differ from ordinary OIDC federation.

However, protocol-domain policies do not automatically express institution-specific accreditation or business rules. The Trust Marks shown for eduGAIN membership, REFEDS R&S, and SRAM are not connected to particular entities or policy decisions in the diagram.

**Recommendation:** Define domain-level metadata policies, the accepted Trust Mark types and issuers, applicability rules for each entity role, and any additional organization-specific constraints.

### 4.8 Protocol support

**Evaluation question:** How naturally does each topology support OIDC, OAuth, OpenID4VCI, and OpenID4VP without duplicating trust infrastructure?

| Topology | OIDC | OAuth | OpenID4VCI | OpenID4VP |
|---|---|---|---|---|
| H1 | Natural for OP/RP federation | Possible with appropriate entity metadata | Possible with appropriate Credential Issuer metadata | Possible with appropriate Credential Verifier metadata |
| H2 | Natural | Possible | Possible | Possible |
| H3 | Natural | Possible | Possible | Possible |

The distinction is between **protocol capability and architectural organization**. OpenID Federation is not restricted to the hierarchy represented by any one diagram.

H1 can support multiple roles, but its diagram does not explicitly model separate Issuer entities for every organization. Combined OP/Issuer nodes are not a substitute for correctly defined metadata for each role.

H2 supports multiple roles under a shared hierarchy, but organizational delegation is applied only to selected RPs and Verifiers.

H3 explicitly groups OP and Issuer entities for Univ B and eduID. This is helpful where those organizations require a common organizational authority, but it does not establish a general mechanism for every protocol combination.

None of the diagrams fully demonstrates coverage of every role in the requested protocols. For example, OAuth Authorization Servers, OAuth Clients, Protected Resources, Wallet Providers, and Wallet Relying Parties are not all explicitly represented as separate entity roles.

**Assessment:** H2 and H3 can support the requested protocols, but correct implementation requires the appropriate entity types, metadata, and trust-evaluation procedures. A topology diagram alone does not establish protocol completeness.

#### H4 — Protocol-specific intermediates

H4 explicitly separates conventional OIDC OP/RP entities from wallet Issuer/Verifier entities, making protocol-specific policy and operational treatment clear. This is a useful conceptual fit if the OIDC and wallet domains have distinct metadata requirements and release processes.

The diagram alone does not establish complete protocol support. It must still define the required entity metadata, endpoints, keys, policies, and wallet-specific trust evaluation. It also does not show whether entities can legitimately participate in both domains, how cross-domain relationships are represented, or whether one organization has separate Federation Entity identifiers for its separate roles.

**Assessment:** Strong structural separation by protocol; implementation conformance must be validated independently against the applicable specifications.

### 4.9 Federation composition

**Evaluation question:** Can the federation be nested, interconnected, or combined with other federations?

| Topology | Assessment |
|---|---|
| H1 | Simple central federation, but cross-federation composition is not explicitly shown. |
| H2 | Organizational Intermediates provide a natural starting point for nested federation structures. |
| H3 | Similar to H2, with additional organizational trust boundaries. |

H1 is straightforward when there is one Trust Anchor and one trust domain. Extending it to multiple federations may require additional relationships or an explicit mechanism for recognizing external Trust Anchors.

H2 and H3 support hierarchical delegation and provide organizational boundaries that can be useful when connecting separate federations.

For all four, multiple federation membership requires a deliberate trust model. An entity participating in multiple federations must establish valid Trust Chains to the relevant Trust Anchors, and relying parties must apply the policies of the intended trust domain.

**Recommendation:** H2 or H3 if composition is driven by organizational delegation. For genuine federations-of-federations, explicitly show external Trust Anchors, superior/subordinate relationships, and intended trust paths.

#### H4 — Protocol-specific intermediates

H4 provides a clear internal split between OIDC and wallet federation, but does not show external Trust Anchors, cross-federation relationships, or a federation-of-federations model. Its two Intermediates are both subordinate to the same SURFconext Trust Anchor, so the diagram describes two domains within one hierarchy rather than independently governed federations.

If wallet entities need to trust external Credential Issuers or Verifiers, or if OIDC participants belong to other federations, show the relevant Trust Anchors and valid Trust Chains explicitly.

**Assessment:** Moderate. Domain separation is clear, but broader federation composition remains unspecified.

### 4.10 Operations

**Evaluation question:** How difficult is onboarding, offboarding, key rotation, policy change, and organizational restructuring?

| Topology | Operational strengths | Operational weaknesses |
|---|---|---|
| H1 | Few hierarchy layers; straightforward central oversight. | Every entity is managed through a relatively centralized model. |
| H2 | Organizational Intermediates can manage subordinate entities. | Different onboarding procedures may be required for direct and delegated entities. |
| H3 | Dual-role organizations can potentially manage both roles under a common Intermediate. | More federation components and delegation rules. |

H1 has low initial structural complexity, but the Trust Anchor's workload grows as the federation adds entities.

H2 shifts some work to organizational Intermediates. This can reduce central coordination if each organization has the staff, infrastructure, and procedures to manage subordinates.

H3 can simplify operations for dual-role organizations if the shared Intermediate genuinely reduces duplication rather than creating another administrative layer.

**Recommendation:** H2 is a strong general-purpose operational model. H3 is attractive when dual-role organizations benefit from common administration.

#### H4 — Protocol-specific intermediates

H4 can simplify top-level administration because SURFconext directly handles two Intermediate relationships, while the domain authorities handle onboarding and metadata operations for their respective roles. It can also enable protocol-specific release schedules and operational playbooks.

The operational load is concentrated in two authorities serving many organizations. Ownership, service-level objectives, on-call responsibility, audit logs, onboarding approvals, and entity offboarding must be explicit. If the Intermediates are operated centrally, this is centralization at the domain level rather than institutional decentralization.

**Assessment:** Moderate–Strong where protocol-domain operations are genuinely shared; less attractive where each institution expects to administer its own entities.

### 4.11 Auditability

**Evaluation question:** Can the federation explain why entity A trusted entity B at a particular time?

All four topologies can support technical auditability through signed Entity Statements and Trust Chain construction. The key question is whether enough evidence is preserved to reconstruct a decision.

| Topology | Auditability strengths | Additional requirements |
|---|---|---|
| H1 | Relatively short trust paths and fewer Intermediate-level statements. | Retain Trust Anchor decisions, policies, Trust Marks, and historical key information. |
| H2 | Delegation can be traced through organizational Intermediates. | Record Intermediate-issued statements and policy changes. |
| H3 | Organizational boundaries can make responsibility easier to trace. | Maintain records for role-specific entities and shared organizational authorities. |

For a reproducible decision, preserve or be able to retrieve:
- The entity's Entity Configuration at the relevant time.
- The Subordinate Statements forming the Trust Chain.
- The applicable Trust Anchor and validation keys.
- The metadata and policies used to evaluate the entity.
- Relevant Trust Marks and their status.
- The resolution result and its validity period.
- Historical key and revocation information where applicable.

The topology alone cannot provide this evidence; it must be captured by logging, retention, and audit processes.

**Assessment:** H1 offers simpler paths; H2 and H3 provide more explicit organizational accountability. None has a decisive inherent advantage.

#### H4 — Protocol-specific intermediates

H4 provides a clean audit split: a trust decision can be traced through either the OIDC or wallet Intermediate and then to the Trust Anchor. That can make protocol-level responsibility and policy application easier to review.

However, because each Intermediate spans multiple organizations, audit records must identify the organization responsible for each leaf entity and the person or process approving changes. Retain historical Entity Configurations, Subordinate Statements, Trust Marks, policies, key status, and resolver outputs sufficient to reconstruct decisions at a given time.

**Assessment:** Strong for domain-level traceability, provided organizational ownership is recorded separately.

### 4.12 Privacy

**Evaluation question:** What information about entities, relationships, roles, and organizational structure is exposed?

H1 shows all participating entities at the Trust Anchor level. This makes the structure easy to inspect but makes membership relationships visible to the central operator.

H2 reveals the organizational Intermediate structure and subordinate relationships beneath SURF and Nikhef. This can improve administrative separation, although it also reveals organizational relationships to parties able to access the relevant metadata.

H3 additionally reveals that Univ B and eduID operate both OP and Issuer services under dedicated Intermediates.

Hierarchical federation does not automatically guarantee privacy. Disclosure depends on which Entity Statements, subordinate listings, Trust Chains, and resolution endpoints are publicly accessible or restricted.

**Assessment:** H2 and H3 offer clearer organizational separation, but neither guarantees reduced metadata disclosure. Define which organizational relationships must be public and which metadata needs to be available to resolvers.

#### H4 — Protocol-specific intermediates

H4 separates metadata and trust administration by role domain, but this does not necessarily reduce the amount of personal or organizational information disclosed. All OIDC entities are grouped under one authority and all wallet entities under another; whether subordinate listings or metadata are publicly retrievable is an implementation choice.

The split may reveal which organizations operate wallet Issuers or Verifiers, and the diagram makes role relationships visible. Define the minimum metadata needed for resolution and whether any entity relationships should be access-controlled where the specifications and use case permit it.

**Assessment:** Conditional. Protocol separation is not itself a privacy control.

### 4.13 Performance

**Evaluation question:** How much latency, bandwidth, computation, and metadata retrieval are required to establish trust?

A useful starting point is the number of statements that must be obtained and validated to construct a Trust Chain.

- A direct leaf-to-Trust Anchor chain has fewer hierarchy levels.
- A leaf beneath an Intermediate requires an additional delegation statement.
- More deeply nested federations require additional statements for intervening authorities.

| Topology | Expected performance characteristics |
|---|---|
| H1 | Shortest depicted trust paths; more entities may be managed directly by the Trust Anchor. |
| H2 | Longer paths for subordinate entities; direct members retain shorter paths. |
| H3 | Similar to H2, with additional organizational Intermediates. |

These differences are not enough to predict actual latency. A resolver that caches valid statements may outperform a direct-resolution implementation that repeatedly retrieves statements. Conversely, a heavily loaded Intermediate or resolver may introduce latency even with a short Trust Chain.

**Assessment:** H1 has the shortest depicted paths. H2 and H3 add hierarchy depth for delegated entities. Quantitative comparison requires measurements of statement retrievals, cache-hit rate, validation time, metadata size, and resolver latency.

#### H4 — Protocol-specific intermediates

Every leaf entity in H4 is one Intermediate level below the Trust Anchor, so its depicted Trust Chain is longer than a direct H1 relationship and similar in depth to a one-level organizational Intermediate. The domain authorities may also become busy resolution and statement-serving endpoints.

Caching, statement validity, resolver placement, metadata size, concurrency, and Trust Chain validation determine real latency more than the diagram alone. Load-test `IA_OIDC` and `IA_Wallet` independently, including failure and cache-expiration scenarios.

**Assessment:** Moderate. Predictable hierarchy depth is helpful, but the central domain authorities may become performance hot spots.

### 4.14 Resilience

**Evaluation question:** How does the federation behave during partial failures, network partitions, stale metadata, or compromised components?

H1 has relatively few authority layers, but its centralized model means Trust Anchor problems can affect the whole federation.

H2 and H3 introduce organizational Intermediates, creating additional dependencies while also allowing some administrative and operational isolation.

In all four, resilience depends on handling stale or expired statements, key compromise, revocation, and temporary resolution failures.

A resilient implementation should:
- Cache validated statements and resolved metadata within their validity periods.
- Operate critical federation endpoints redundantly.
- Define how stale information is handled during an outage.
- Establish procedures for revoking compromised keys and authorities.
- Ensure recovery does not silently accept invalid or expired Trust Chains.
- Test the effect of losing a Trust Anchor, an Intermediate, or a resolver.

**Assessment:** H2 and H3 provide opportunities for organizational isolation; H1 has fewer Intermediate dependencies. None is inherently resilient without appropriate infrastructure and recovery procedures.

#### H4 — Protocol-specific intermediates

H4 has two clear failure domains, but each failure domain covers many entities. If `IA_OIDC` becomes unavailable, all OIDC subordinates may be affected when fresh statements are needed; if `IA_Wallet` fails, the wallet participant set may be affected. Cached, still-valid statements can help only within the security policy's freshness and revocation constraints.

Operate each Intermediate redundantly, avoid shared infrastructure that makes both fail together, and test emergency key replacement, statement expiry, revocation, and recovery. Consider whether domain-level disaster recovery can be performed without compromising the other domain.

**Assessment:** Conditional; two-domain separation helps isolation, while broad subordinate sets increase the impact of an individual domain outage.

### 4.15 Migration

**Evaluation question:** How easily can an entity, Intermediate, or federation be moved or reorganized?

| Topology | Migration strengths | Migration challenges |
|---|---|---|
| H1 | A leaf can potentially be moved by changing its direct federation relationship. | The Trust Anchor must coordinate the change for each affected entity. |
| H2 | Entities can be reassigned between organizational Intermediates. | Old and new trust relationships must transition consistently. |
| H3 | Dual-role organizations can be reorganized around explicit organizational boundaries. | Moving or replacing an organizational Intermediate can affect both OP and Issuer roles. |

Changing federation topology can cause temporary Trust Chain validation errors during transitions. Migration requires coordination of Entity Configurations, Subordinate Statements, authority hints, keys, and metadata.

H1 is conceptually straightforward for a small number of entities, but bulk migrations increase coordination work at the Trust Anchor.

H2 and H3 allow organizational restructuring to be expressed through changes in delegation. H3 is particularly useful when an organization needs to move identity and credential services together.

**Assessment:** H2 and H3 are good fits for organization-driven restructuring; H4 can suit protocol-domain restructuring. All require a migration plan that preserves valid trust paths during transition.

#### H4 — Protocol-specific intermediates

H4 makes it straightforward to add entities to the relevant protocol domain, but migration between domains is more than a simple organizational move. An entity's Federation Entity identifier, role metadata, subordinate relationship, and Trust Chain must be handled consistently. Moving an organization's services between domain authorities can affect multiple entities at once.

Because an institution's OP and Issuer are under different Intermediates, organizational restructuring may require coordinated changes in both domains. Plan transitions with overlap, validation of old and new Trust Chains, and a clear rollback path.

**Assessment:** Moderate. Adding leaves is simple; moving authority boundaries or changing an entity's role/domain requires careful coordination.

### 4.16 Legal / regulatory

**Evaluation question:** Can responsibilities for onboarding, accreditation, credential issuance, verification, and federation operation be attributed clearly?

| Topology | Potential accountability advantage | Principal concern |
|---|---|---|
| H1 | A central authority has a clear role in federation membership and policy. | Centralized responsibility may be difficult to scale across many institutions. |
| H2 | Organizational Intermediates can make delegated responsibility explicit. | Responsibilities between SURFconext and each organization must be documented. |
| H3 | Dual-role organizations can have a common organizational authority for identity and credential services. | Shared authority must not obscure distinct obligations for different services. |

H1 is straightforward when SURFconext is intended to be the principal federation operator and retains membership decisions.

H2 can make institutional accountability clearer if SURF and Nikhef are formally responsible for entities subordinate to their Intermediates.

H3 can support a common institutional governance model for organizations providing both OP and Issuer services. However, legal obligations for identity providers and credential issuers may differ even within one organization.

For all four, the federation agreement should define:
- Who approves membership and accreditation.
- Who is responsible for the accuracy of published metadata.
- Who manages keys and responds to security incidents.
- Who can suspend or revoke membership.
- How credential issuance and verification responsibilities are allocated.
- How personal data, audit records, and cross-border obligations are handled.

**Assessment:** H2 and H3 provide a strong structural basis for organizational accountability; H4 can provide protocol-domain accountability if responsibilities are formalized. Actual legal compliance cannot be inferred from the topology alone.

#### H4 — Protocol-specific intermediates

H4 can align responsibility with protocol operations: one authority may be accountable for OIDC federation requirements and another for wallet federation requirements. That can be useful if contracts, accreditation schemes, and incident responsibilities are genuinely organized by protocol domain.

However, legal and regulatory accountability is often tied to organizations and services rather than protocol categories. Since each Intermediate spans multiple institutions, the architecture should identify who is responsible for each entity's metadata, credential issuance, verification, security incidents, and compliance evidence. A Trust Mark is not by itself a complete legal allocation of responsibility.

**Assessment:** Moderate–Strong if domain-level responsibilities are formally defined; otherwise it risks obscuring institution-level accountability.

### 4.17 Implementation complexity

**Evaluation question:** How difficult is the topology to implement, secure, and operate?

| Topology | Relative complexity | Main sources of complexity |
|---|---|---|
| H1 | Low initially | Central management, direct entity configuration, Trust Anchor operations. |
| H2 | Moderate | Organizational Intermediates, delegated statements, policy management. |
| H3 | Moderate–High | Additional Intermediates and coordination of dual-role organizations. |

H1 is easiest to understand and likely has the lowest initial implementation burden, assuming a limited number of entities and acceptable centralized management.

H2 adds organizational Intermediates and delegated relationships. This introduces additional key management and policy enforcement, but may reduce long-term administrative effort.

H3 adds more federation entities and delegation relationships. The complexity is justified when the organizational grouping simplifies policy administration or provides a meaningful security boundary.

The number of entities alone is not a sufficient measure of complexity. A larger number of simple, independently managed entities may be easier to operate than a smaller number of highly privileged Intermediates with complicated policies.

**Assessment:** H1 has the lowest initial complexity. H2 offers a reasonable balance between complexity and delegation. H3 requires more design and operational coordination, but can provide clearer separation of responsibilities.

#### H4 — Protocol-specific intermediates

H4 has only two direct subordinate authorities at the Trust Anchor level, which simplifies the top-level picture. It nevertheless requires two well-operated Intermediate services, separate domain policies, careful onboarding rules, and clear handling of organizations whose services span both domains.

Its complexity is moderate rather than minimal: fewer top-level relationships do not remove the operational burden, but move much of it into the two domain authorities. If protocol policies differ substantially, this may be simpler than repeated organization-specific exceptions. If organizations need independent administration, the model may need additional layers.

**Assessment:** Moderate. H4 is a good fit when the organization can sustain two domain-wide authorities and wants a clean protocol split.

### 4.18 OpenID Federation capabilities

**Evaluation question:** How well does each topology use Trust Chains, Trust Marks, and the resolver endpoint?

| Capability | H1 | H2 | H3 | H4 |
|---|---|---|---|---|
| Trust Chain construction | Direct chains for depicted members | Chains through organizational Intermediates | Chains through organizational and dual-role Intermediates | Chains through either `IA_OIDC` or `IA_Wallet` |
| Trust Marks | Issuers are identified, but issuance and validation are not shown | Same | Same | Same; applicability to entities is not shown |
| Resolver endpoint | Resolution is mentioned, but endpoint ownership and behavior are unspecified | Same | Same | TA resolves two Intermediates; each Intermediate resolves its leaves, but operation semantics are unspecified |
| Metadata policies | Not shown | Potentially applied at organizational levels | Potentially applied at organizational and role-grouping levels | Potentially applied by protocol domain |
| Multiple Trust Anchors | Not shown | Not shown | Not shown | Not shown |
| Historical keys and revocation | Not shown | Not shown | Not shown | Not shown |

All four topologies can use the core protocol capabilities. Differences arise from how those capabilities are organized.

- **H1:** Supports straightforward Trust Chain construction for directly registered entities, but does not show the statements, policies, or Trust Mark validation needed for a complete trust decision.
- **H2:** Adds Subordinate Statements and enables organizational policy enforcement. Delegation can be traced through the Trust Chain.
- **H3:** Adds organizational Intermediates for dual-role entities. It can support organizational policy enforcement across OP and Issuer entities, but does not automatically provide common policy or trust status for both roles.
- **H4:** Adds protocol-domain Intermediates. It can support OIDC-specific and wallet-specific policy enforcement, but does not automatically provide institution-specific delegation or shared authority across an institution's OP and Issuer entities.

The diagrams do not show whether Trust Marks are issued and validated, whether a shared or separate resolver is used, or how key rollover and revocation are handled.

**Assessment:** All four are compatible in principle with OpenID Federation's core mechanisms. H2 and H3 emphasize organizational delegation; H4 emphasizes protocol-domain delegation. Those benefits must be implemented explicitly through valid statements, policies, Trust Marks, and operational controls.

---

#### H4 — Protocol-specific intermediates

H4 can use Trust Chains from leaf entities through `IA_OIDC` or `IA_Wallet` to the SURFconext Trust Anchor. It can also use metadata policies at the domain Intermediate level. The diagram labels eduGAIN, REFEDS R&S, and SRAM as Trust Mark Issuers, but does not show issuance, applicability, validation, or revocation of those Trust Marks.

The diagram also says the Trust Anchor fetches/resolves metadata for the two Intermediates, and that each Intermediate fetches/resolves its members. This operational description should be distinguished from the actual federation hierarchy and Trust Chain construction. Specify whether these arrows mean retrieval of Entity Configurations, Subordinate Statements, resolver queries, or other metadata operations.

**Assessment:** Strong in principle for domain-specific delegation, but the required signed statements, metadata policies, Trust Mark processing, endpoint behavior, and key/revocation procedures remain to be specified.

## 5. Topology-specific issues to address

The following issues should be resolved before selecting a topology.

### 5.1 H1: Clarify combined OP and Issuer entities

H1 models Univ B and eduID as combined OP/Issuer entities, while the other organizations have separate OP entities and no explicitly depicted Issuer entities.

This creates ambiguity about whether:
- An OP/Issuer is a single Federation Entity with multiple protocol roles.
- The organization operates separate Federation Entities for OP and Issuer services.
- Both services share a common organizational trust authority.

These alternatives have different implications for keys, policies, metadata, and accountability.

**Recommendation:** Represent an organizational identity separately from its protocol entities where that distinction matters. If an organization operates two distinct services, show their relationship explicitly unless the design intentionally uses one Federation Entity with multiple roles.

### 5.2 H2: Make organizational delegation consistent

H2 introduces organizational Intermediates for SURF and Nikhef, but the OPs and Issuers for these organizations are direct members of the Trust Anchor.

The organizational Intermediate therefore does not represent the entire organization; it represents only a subset of its federation participants.

That is not necessarily incorrect, but it raises a governance question: who controls the OP and Issuer entities, and how does that control relate to the Intermediate?

If SURF and Nikhef are intended to have organizational autonomy over all their services, show how their OP and Issuer entities participate in that delegated structure. If direct membership is intentional, document why some roles are delegated while others remain under direct Trust Anchor control.

### 5.3 H3: Define the purpose of IA_B and IA_EDUID

H3 introduces `IA_B` and `IA_EDUID` to group the OP and Issuer entities belonging to Univ B and eduID.

This is useful if the Intermediates represent real organizational trust authorities. If they exist only to group two services visually, they introduce additional signing keys, Subordinate Statements, and operational responsibilities without necessarily providing a meaningful trust benefit.

**Recommendation:** Define each Intermediate's responsibilities explicitly:
- Who controls its signing key?
- Who approves subordinate entities?
- Which policies can it enforce?
- Can it independently suspend or remove a subordinate?
- Does it represent a legal organization or a delegated service domain?

If the organization manages its own services under a common authority, H3 has a clear rationale. Otherwise, a simpler representation may suffice.

### 5.4 H4: Define the scope and governance of the two protocol Intermediates

H4 introduces `IA_OIDC` and `IA_Wallet`, each serving multiple organizations. Before adopting this structure, define:

- Whether each Intermediate is operated by SURFconext or by a separate governance body.
- Who approves onboarding, suspension, and removal of each subordinate entity.
- Which metadata policies apply at each domain level and whether organization-specific constraints are needed.
- How each Intermediate's signing keys are protected, rotated, and recovered after compromise.
- Whether a domain Intermediate may delegate further, and how that authority is constrained.
- How entities that offer services in both domains are represented and audited.
- Whether domain-wide outages or policy changes can be isolated from the other domain.

A protocol-domain split is not the same as organizational delegation. H4 should be chosen because protocol-specific governance is the desired authority model, not merely because it produces a tidy diagram.

### 5.5 All topologies: Clarify Trust Mark relationships

All four diagrams show eduGAIN Membership, REFEDS Research and Scholarship, and SRAM as Trust Mark Issuers. The arrows alone do not show how Trust Marks are issued, trusted, validated, or revoked.

Distinguish:
1. The entities that issue Trust Marks.
2. The Trust Mark types recognized by SURFconext.
3. The entities to which each Trust Mark applies.
4. The mechanisms used to validate Trust Mark status.
5. The policies that determine whether a Trust Mark is required for a particular role.

A participant might be a federation member but not hold the accreditation required to act as a Credential Issuer. This distinction is especially important for wallet architectures, where trust in a Credential Issuer and its entitlement to issue a particular type of credential are separate questions.

### 5.6 All topologies: Make the resolver architecture explicit

The diagrams use labels such as `fetch/resolve`, but do not specify whether these mean:
- Retrieving an Entity Configuration.
- Retrieving a Subordinate Statement.
- Resolving metadata through a resolver endpoint.
- Validating a Trust Chain locally.
- Retrieving or validating Trust Marks.

These operations are related but distinct.

For architectural clarity, show the **trust hierarchy** separately from the **metadata-resolution architecture**. This prevents operational arrows from being confused with federation membership or delegation relationships.

---

## 6. Recommendations under different requirements

There is no universally optimal topology. The following recommendations reflect different priorities.

| Primary requirement | Recommended topology | Rationale |
|---|---|---|
| Minimum initial implementation complexity | **H1** | Fewest hierarchy layers and a straightforward central governance model. |
| Organizational autonomy | **H2** | Explicit delegation to organizational Intermediates without adding separate role-grouping authorities for every organization. |
| Shared organizational control of OP and Issuer services | **H3** | Explicitly groups the two roles under a common organizational Intermediate. |
| Large federation with many institutional participants | **H2 or H3** | Organizational delegation can reduce the Trust Anchor's direct management workload. |
| Shared organizational policies across identity and wallet roles | **H3** | Provides a possible common organizational policy boundary while retaining role-specific requirements. |
| Separate OIDC and wallet policy/operations | **H4** | Provides a protocol-domain authority for OPs/RPs and a separate authority for Issuers/Verifiers. |
| Future organizational restructuring | **H2 or H3** | Organizational delegation provides a natural starting point for reorganizing trust relationships. |

### Preferred starting point: H2

If the objective is to build a general-purpose SURFconext federation that supports conventional identity federation and wallet use cases, **H2 is a reasonable initial reference architecture, subject to one important refinement**.

H2 balances:
- Central federation governance.
- Delegation of organizational responsibility.
- Support for different entity roles.
- Manageable hierarchy depth.
- Operational complexity.

I would then extend it selectively:
- Adopt the H3 pattern for organizations that genuinely need a shared trust authority across their OP and Issuer services.
- Adopt the H4 pattern if OIDC and wallet participants need separate domain-level governance, policies, and operations and if broad Intermediate authority is acceptable.
- Keep services directly under the Trust Anchor where delegation does not provide a meaningful benefit.

This avoids making every organizational or role distinction a new Intermediate by default.

The choice should follow the intended governance model. If Univ B and eduID do not need common authority across their OP and Issuer services, the extra Intermediates in H3 may not be justified.

---

## 7. Final conclusions

The four designs represent different architectural priorities rather than mutually exclusive ways to implement OpenID Federation.

**H1 prioritizes simplicity.** It suits a federation where SURFconext should retain direct control of membership and extensive organizational delegation is not required.

**H2 prioritizes organizational delegation.** It provides a practical baseline for institutions that need to manage subordinate participants while remaining within a common federation.

**H3 prioritizes organizational grouping across multiple roles.** It is particularly useful when organizations such as Univ B and eduID operate both OP and Issuer services and need a common authority for those roles.

**H4 prioritizes protocol-domain separation.** It is useful when OIDC OP/RP entities and wallet Issuer/Verifier entities should be managed under separate domain authorities. Its principal trade-off is that each authority spans multiple organizations, so organizational accountability and the blast radius of an authority failure must be managed explicitly.

My recommendation is to **use H2 as the baseline when organizational delegation is the primary goal; use H3 selectively for shared organizational control across OP and Issuer roles; and consider H4 when protocol-domain separation is the primary goal**. H4 is not automatically better than H2/H3: it reflects a different choice about where authority and accountability should sit.

Before finalizing the architecture, validate three things:

1. **Trust correctness:** Every depicted relationship must correspond to a valid OpenID Federation trust relationship, with appropriate Entity Configurations, Subordinate Statements, and Trust Chains.
2. **Governance correctness:** Every Intermediate must have a clearly defined purpose, delegated authority, and operational owner.
3. **Operational correctness:** The deployment must define resolution, caching, key rotation, revocation, Trust Mark validation, and recovery from partial failures.

These checks will establish whether each topology can satisfy the federation's trust, governance, and operational requirements.

## Standards references

- [OpenID Federation 1.0 specification](https://openid.net/specs/openid-federation-1_0.html)
- [OpenID Federation for Wallet Architectures 1.0 specification](https://openid.net/specs/openid-federation-wallet-1_0.html)

**Version note:** The wallet specification is versioned independently of the core OpenID Federation specification. Before implementation, verify the exact published versions the project intends to adopt and ensure that entity types, metadata requirements, and trust-evaluation procedures match those versions.
