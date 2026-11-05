# Recap – Federation Topologies H1‑H4  

### H1 – Flat federation
- **Simple hierarchy:** All entities are direct subordinates of the Trust Anchor, giving the shortest trust paths.  
- **Advantages:** Easy to understand, low onboarding friction, and minimal signature processing.  
- **Limitations:** No organisational boundaries; the Trust Anchor must manage every leaf, creating a single point of failure as the federation grows.  
- **Auditability:** Short trust chains simplify logging, but the TA must retain all decisions, policies, Trust Marks, and historical keys for full traceability.  
- **Operational impact:** Low structural complexity, yet the TA’s workload increases linearly with the number of participants.

### H2 – Organizational intermediates
- **Delegated authority:** Intermediates represent organisations and manage their own RPs/Verifiers, providing clearer accountability.  
- **Advantages:** Delegation enables finer‑grained policy enforcement per organisation.  
- **Limitations:** On‑boarding/off‑boarding procedures differ for direct‑TA entities versus delegated ones, adding procedural overhead.  
- **Auditability:** Must record Intermediate‑issued statements and any policy changes to reconstruct decisions.  
- **Operational impact:** Workload is distributed across Intermediates, reducing the TA’s load, but the federation gains additional moving parts.

### H3 – Dual‑role organisational intermediates
- **Combined roles:** Each organisation’s Intermediate groups both its OP and Issuer entities, keeping organisational boundaries explicit.  
- **Advantages:** Boundaries make responsibility tracing easier; dual‑role grouping simplifies internal governance.  
- **Limitations:** Adds more federation components and delegation rules, increasing overall complexity.  
- **Auditability:** Requires records for role‑specific entities and shared authorities to preserve decision evidence.  
- **Operational impact:** Potential for shared management of OP/Issuer but at the cost of extra configuration and coordination.

### H4 – Protocol‑specific intermediates
- **Domain split:** Two Intermediates separate OIDC services from wallet services, allowing protocol‑specific policies and operations.  
- **Advantages:** Clear internal split enables tailored metadata policies per protocol domain.  
- **Limitations:** Does not show external Trust Anchors or cross‑federation links, so broader federation‑of‑federations scenarios are unclear.  
- **Auditability:** Moderate – domain separation is evident, yet lack of external trust‑anchor visibility limits full audit trails.  
- **Operational impact:** Protocol‑level delegation is clear, yet the single Trust Anchor still represents a central dependency for both domains.