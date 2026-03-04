# Architecture Decisions

### Mar 1, 2026 — WhatsApp as Primary Interface
**Type:** decision
**Summary:** WhatsApp over a web app for primary interaction. EIID analysis: enrichment is photo capture (already happens on phone), interpretation fits in a message (recipe card), delivery is conversational. Building a web-first recipe browser would force users out of their natural flow. The web archive exists for reference, not interaction.
**EIID Layer:** delivery
**Action:** WhatsApp Business API webhook as primary entry point. Web archive built as secondary, read-heavy surface.

### Mar 3, 2026 — No Visual Recipe Builder
**Type:** decision
**Summary:** The interpretation layer is conversational-first. Users receive recipe cards, not interactive cooking interfaces. A visual step-by-step builder with timers, animations, and progress tracking would be a different product. It differentiates in the wrong layer: the value is in matching recipes to available ingredients, not in guiding someone through chopping onions.
**EIID Layer:** interpretation
**Action:** Recipe detail page is static content (photo, ingredients, steps). No interactive cooking mode.

### Mar 3, 2026 — Supabase Over Custom Backend
**Type:** decision
**Summary:** Auth, database, and realtime are commodity. Automate. Row-level security handles multi-user data isolation without custom middleware. Development time goes to the WhatsApp interaction patterns, which is where the innovation lives.
**EIID Layer:** none (infrastructure)
**Action:** Supabase for auth, database, and storage. No custom backend services.

### Mar 4, 2026 — Email for Planning, WhatsApp for Real-Time
**Type:** decision
**Summary:** Different delivery cadences for different purposes. Weekly meal plan summary works as email because users read it Sunday evening while planning the week. Individual recipe suggestions work as WhatsApp because the response should arrive while the user is still in the grocery store. Mixing cadences in one channel creates noise: a meal plan in WhatsApp gets buried, a single recipe in email gets ignored.
**EIID Layer:** delivery
**Action:** Resend for weekly Sunday 17:00 meal plan emails. WhatsApp for immediate post-receipt recipe suggestions.
