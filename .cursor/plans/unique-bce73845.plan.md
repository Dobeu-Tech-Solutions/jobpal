<!-- bce73845-cc0c-4f75-846a-f8849138d710 9c5c6881-ba09-4fda-b803-30c830f1b81a -->
# Unique Staffing AI Agent MVP

This phase delivers a working proof-of-concept voice agent that:

1. Reads new applicant records from Supabase (data captured by Netlify site form).
2. Migrates/updates the canonical employee record in MongoDB Atlas.
3. Provides a browser-based multilingual voice interface (English/Spanish/French/Amharic) powered by:
• OpenAI `@openai/agents-realtime` for dialogue + tool delegation.
• ElevenLabs TTS/STT models (Flash v2.5 for output, Scribe v1 for input).
4. Exposes SMS hand-off via Twilio webhook (keep-human-in-loop fallback). 
5. Logs all interactions and produces daily/weekly activity reports.

Key code locations we will create / modify:

- `backend/`: Node server (Express or Next.js API routes) handling Supabase webhooks, MongoDB ops, token generation for realtime agent, Twilio webhooks.
- `voice-agent/`: Vite + TS frontend embedding RealtimeSession.
- `scripts/migrateSupabaseToMongo.ts`: one-off + scheduled migration.
- Config files: `.env`, `supabaseClient.ts`, `mongoClient.ts`.

High-level steps:

1. **Scaffold monorepo structure** (root, packages for backend & frontend).
2. **Integrate databases**: set up Supabase service key env; connect to MongoDB Atlas X.509 user.
3. **Implement migration script & schedule (cron or Supabase edge function).**
4. **Create backend endpoints**:
• `/api/token` → returns ephemeral OpenAI realtime token.
• `/api/supabase-webhook` → receives insert/update; triggers migration.
• `/api/twilio` → SMS webhook; forward to agent / escalate to human.
5. **Voice agent frontend**: minimal page that auto-connects mic, streams voice, renders transcript.
6. **Define Agents & tools**:
• `OnboardingAgent` main.
• `SupabaseTool`, `MongoTool`, `SMSNotifyTool` via MCP server or custom functions.
7. **Reporting**: daily cron job summarizing activity, emailing/storing in Mongo.
8. **Deployment**: Continue Netlify for frontend; Render/Fly for backend API (temporary).
9. **Docs & README** update.

### To-dos

- [ ] Create monorepo folders backend/, voice-agent/, scripts/, and basic package.jsons
- [ ] Implement Supabase and MongoDB client helpers with env-based creds
- [ ] Write migrateSupabaseToMongo.ts and test against staging data
- [ ] Add Express/Next API routes for token, supabase webhook, twilio webhook
- [ ] Build Vite TS app embedding RealtimeSession with ElevenLabs TTS/STT
- [ ] Create OnboardingAgent and helper tools using @openai/agents-realtime
- [ ] Set up cron or GitHub Action that compiles daily/weekly reports
- [ ] Configure Netlify build for frontend and Render/Fly for backend API
- [ ] Update README and docs with setup instructions