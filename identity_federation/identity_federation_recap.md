# Recap – Scenario Comparisons A‑C  

### Scenario A – Full hierarchy (TA → IAs → OPs & RPs)
- **Trust‑chain depth:** 3 hops for IA‑managed RPs, 2 hops for leafs attached directly to the TA.  
- **Fine‑grained policies:** Each IA can enforce its own metadata policies, preventing global changes from breaking sub‑communities.  
- **Scalability:** Load is distributed; the TA only serves statements for each IA and any directly attached OPs, scaling well with a modest number of IAs.  
- **Operational manageability:** Clear ownership and delegated administration, though the extra layer adds complexity.  
- **Failure isolation:** An IA outage affects only its subtree; the rest of the federation remains functional.

### Scenario B – Hybrid (OP‑under‑IA when IA exists)
- **Trust‑chain depth:** Same as A, but 3‑hop chains exist only for organisations that already run an IA.  
- **Mixed governance:** IA‑managed leafs keep local policy control, while directly attached OPs inherit the TA’s global policies, simplifying smaller entities.  
- **Scalability:** Very good – bulk of the load is off‑loaded to existing IAs, while the TA handles only non‑IA entities.  
- **Operational trade‑off:** Two governance models coexist, which can cause inconsistencies in metadata policies and Trust‑Mark handling.  
- **Failure domains:** TA remains a single point of failure only for directly attached leafs; IA outages remain isolated.

### Scenario C – Flat hierarchy (all leafs under the TA)
- **Uniform trust‑chain:** Every entity has a 2‑hop chain (leaf → TA).  
- **Single global policy:** All leafs share one TA‑wide metadata policy, making auditing easy but any change impacts the entire federation.  
- **Scalability bottleneck:** The TA must host all subordinate statements (hundreds of OPs and thousands of RPs), becoming a performance and reliability hotspot.  
- **Operational load:** High on the TA (key management, revocation, policy updates) with no delegation, increasing risk of bottlenecks.  
- **Failure risk:** The TA is the only availability domain; any outage affects the entire federation.