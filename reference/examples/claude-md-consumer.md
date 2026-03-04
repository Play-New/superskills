# RecipeBox

## Business
**Client:** Solo developer
**Industry:** Consumer, food/cooking, freemium model. Revenue from premium recipe collections and meal planning features.

## User
**End user:** Home cook, 25-45, photographs groceries on the go, interacts primarily through WhatsApp
**Need:** Know what to cook with what's available, not browse recipe databases after a long day

## Stack
Next.js (web archive), Supabase (auth + database + row-level security), Resend (email summaries), WhatsApp Business API (primary interaction channel)

## EIID

### Enrichment (collect)
**Have:** User-submitted photos of grocery receipts and pantry contents via WhatsApp
**Human input:** Photos sent through WhatsApp. No forms, no manual ingredient entry.
**Missing:** Barcode scanning for packaged items, expiration date tracking
**Connect:** WhatsApp Business API webhook for photo intake, OCR service for receipt parsing, image recognition for pantry items
**Approach:** Automate — receipt OCR and ingredient detection are commodity services. No competitive advantage in building custom image processing.

### Inference (patterns)
**Detect:** Available ingredients from parsed receipts and pantry photos
**Predict:** Recipe matches based on ingredient availability, dietary preferences, prep time constraints
**Flag:** Missing key ingredients (one item away from a complete recipe)
**Approach:** Automate — ingredient matching and recipe scoring are standard ML problems. Off-the-shelf similarity scoring works.

### Interpretation (insights)
**Surface:** "You have 8 of 10 ingredients for chicken tikka masala. 35 min prep. 420 cal per serving."
**Frame as:** Recipe card with nutrition, prep time, difficulty, ingredient availability percentage. Conversational format for WhatsApp, expanded format for web archive.
**Approach:** Differentiate — the framing is the product value. How a recipe is presented (availability match, prep time, nutrition context) determines whether someone cooks it.

### Delivery (reach)
**Channels:** WhatsApp (primary, bidirectional — same channel used for input), email (weekly meal plan summary), web archive (saved recipes, read-only browsing)
**Triggers:** New receipt photo processed, weekly meal plan generation (Sunday 17:00), recipe saved by user
**Timing:** Recipe suggestions within 60 seconds of photo processing. Weekly summary on Sunday evening.
**Approach:** Innovate — WhatsApp-native recipe experience. No competitor delivers recipe suggestions back into the same conversation where groceries are photographed.

## Technology Constraints
Use Supabase, NOT Firebase, AWS Amplify.
Use Resend, NOT SendGrid, Mailgun.
WhatsApp message limit: 1024 chars for text, interactive message constraints for buttons and lists.
Image processing latency target: <5s from photo receipt to parsed ingredients.

## Code Architecture
- API routes for WhatsApp webhook handling and message dispatch
- Background jobs for image processing and OCR (async, not blocking the webhook response)
- Web routes for archive browsing and settings management
- Shared types between WhatsApp message formatting and web recipe display
- Supabase row-level security for all user data access

## Design System
**Framework:** Tailwind CSS
**Token source:** globals.css
**Direction:** Warm and editorial. Generous whitespace, serif display type, food photography as primary color energy. Opposite of dense dashboard aesthetic.
**Navigation:** Minimal. Recipes, Collections, Settings. Most interaction happens in WhatsApp, not the web.
