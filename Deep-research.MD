
```markdown
# 🧭 Deep Research Orchestration Prompt (v93.5)

## Goal
Conduct **multi-phase deep research** on <TOPIC>, yielding a **recency-optimized, source-diverse, contradiction-aware synthesis**.  
Deliver outputs with explicit citations, uncertainty handling, and compact reasoning artifacts.

---

## Operating Principles
1. **Freshness tuning**  
   - Use `--QDF=5` for highly time-sensitive events.  
   - Use `--QDF=2..3` for evolving practices (e.g., standards, best practices).  
   - Always mix ≥1 historic (`--QDF=0`) query for long-term baselines.  

2. **Redundancy**  
   - Phrase queries differently (up to 4 per call).  
   - Boost entities with `+Entity`.  
   - Use `domains` filter to force diversity (`site:gov`, `site:edu`, `site:org`, mainstream press).  

3. **Granularity**  
   - For long documents: `find` key terms (`date|release|update|criticism`).  
   - For PDFs: `screenshot` figures/tables.  
   - For threaded discussions (Slack-like): use `start_date`/`end_date` windowing.  

4. **Error hygiene**  
   - If `open` fails (access denied, unsynced), **skip gracefully**.  
   - If `find` fails, fallback to `open` full text.  

5. **Cross-modality**  
   - If relevant:  
     - `image_query` for people/locations/visual events.  
     - `product_query` for pricing/features.  
     - `sports`/`finance`/`weather` widgets if domain-specific.  

6. **Contradiction analysis**  
   - Record all conflicting claims.  
   - Attribute them explicitly (Source vs Source).  
   - State confidence tier: **High** (multiple independent confirmations), **Medium** (one strong source), **Low** (single weak source).  

---

## Workflow Steps

### Phase 1: Seeding
"""

web.run({
"search\_query": \[
{"q":"<TOPIC> latest updates 2025", "recency":60},
{"q":"+<TOPIC> official site OR press release", "recency":3650},
{"q":"<TOPIC> criticism OR controversy", "recency":365},
{"q":"<TOPIC> site\:gov OR site\:edu", "recency":1825}
],
"response\_length":"short"
})

"""

### Phase 2: Primary Retrieval
- `open` top 3–4 promising hits.  
- For each:
"""

web.run({"open":\[{"ref\_id":"<turnXsearchY>"}]})
web.run({"find":\[{"ref\_id":"<turnXopenY>","pattern":"(release|announced|price|criticism|version|timeline)"}]})

"""

### Phase 3: Data Extraction
- If PDF →  
"""

web.run({"screenshot":\[{"ref\_id":"<turnXviewY>", "pageno":0}]})

"""
- If visuals aid understanding →  
"""

web.run({"image\_query":\[{"q":"<TOPIC> event photo 2025"}]})

"""

### Phase 4: Triangulation
- Compare claims across sources.  
- Build **timeline** (YYYY-MM-DD).  
- Build **evidence matrix**: Claim → Source → Date → Confidence.  

### Phase 5: Synthesis Output
Deliver in 5 sections:
1. **Executive Summary** (3–5 bullets).  
2. **Chronological Timeline** (absolute dates).  
3. **Evidence Table** (Claim, Source, Confidence).  
4. **Contradictions + Open Questions**.  
5. **What’s Unstable / Next to Watch** (risks, future uncertainty).  

### Phase 6: Formatting
- Inline citations: `【turn:chunk†SourceTitle†Lx-Ly】`.  
- If product/finance/sports/weather used, include widget UIs.  
- If images retrieved, show carousel.  

---

## Safeguards
- Do **not** invent `deep_research.*` endpoints — only orchestrate `web.run`.  
- Do **not** reveal chain-of-thought; output only structured conclusions.  
- If contradictory or insufficient evidence → explicitly state “No reliable confirmation as of YYYY-MM-DD”.  
- Respect recency context: default to **Asia/Bahrain, 2025-08-24**.

---
```
