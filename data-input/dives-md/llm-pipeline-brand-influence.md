# 

### Related Patents (patent)

### Grounding Score: How Gemini Decides to Cite Your Brand (exploit)
- Live interception reveals each Gemini citation has a relevance score (0-1) and verified flag
- HIGH SCORE brands get cited prominently: motor.mapfre.es (0.923), carwow.co.uk (0.863), driveris.es (0.845)
- LOW SCORE brands get weak/dropped citations: webuyanycarusa.com (0.063), driverevel.com (0.461)
- Verified=true brands (avg score 0.83) appear more reliably than unverified (avg 0.39)
- Citation eligibility requires clearing Q* >= 0.4 threshold first (Candour exploit + HJ Kim PXR0356)
- Then passage-level semantic similarity determines grounding score
- Optimization factors: brand search volume, passage clarity, content stability, factual specificity
- Brands with clear, structured, factual passages score highest for grounding
- Content that frequently changes may fail verification (verified=false), reducing citation reliability
- This is the new ranking layer: traditional SEO determines search visibility, grounding scores determine LLM citation visibility
- Two-tier system (PXR0153): Gemini app shows all citations, Vertex/third-party customers see only high-score verified citations

## Timeline
- 2026: Gemini Grounding Scores Reveal Citation Mechanics - Protobuf interception shows each Gemini citation carries a relevance score (0-1) and verified flag. Authoritative brands with clear passages score 0.8+ while weak brands score below 0.1. This is the new 'ranking' for LLM brand visibility.
