<!-- 2cc5a943-b01e-4b8c-a140-b74350b29a00 20b87b9b-c1b1-44b9-8876-286bd2dd1df3 -->
# JobPal AI Agent Development Plan

I have reviewed the project overview and the existing `unique-staffing-prof` repository. Here is the plan to build the HR onboarding and compliance agent.

## 1. Project Setup & Structure

- [ ] **Clone Repository**: Clone `Dobeu-Tech-Solutions/unique-staffing-prof` into the current workspace to access the frontend and existing Supabase configuration.
- [ ] **Initialize Backend**: Create a new `backend` directory (or separate service) for the **JobPal Agent**. This will be a Node.js/TypeScript service.
- [ ] **Dependency Management**: Initialize `package.json` for the backend with dependencies for `supabase-js`, `mongodb`, `openai`, and `elevenlabs-node`.

## 2. Database Integration

- [ ] **Supabase Sync**: Connect to the `uniquestaffingprofessionals` Supabase project.
    - [ ] Run `SUPABASE_LIST_TABLES` to audit existing schema.
    - [ ] Create `agent_tasks` and `candidate_status` tables to track onboarding progress.
- [ ] **MongoDB Setup**:
    - [ ] Configure MongoDB connection using the provided credentials (stored securely in `.env`).
    - [ ] Implement a migration script/worker to transfer completed applicant data from Supabase to MongoDB.

## 3. AI Agent Core (OpenAI & ElevenLabs)

- [ ] **Agent Logic (OpenAI)**:
    - [ ] Implement the "HR Coordinator" assistant using OpenAI Agents/Assistants API.
    - [ ] Add capabilities to parse and validate the Adobe PDF forms (vision/file search).
- [ ] **Voice Interface (ElevenLabs)**:
    - [ ] Integrate ElevenLabs for text-to-speech (TTS) for outgoing calls/voice interactions.
    - [ ] Setup the voice agent to handle "verbal confirmation" and "troubleshooting" calls.
    - [ ] *Optional*: Explore ElevenLabs Conversational AI for full duplex voice if needed.

## 4. Workflows & Reporting

- [ ] **Trigger System**:
    - [ ] Create webhooks to listen for "Verbal Confirm" events (likely a status change in Supabase or a manual trigger).
- [ ] **Compliance Loop**:
    - [ ] Build the "Human-in-the-loop" notification system (SMS/Email alerts to recruiters when the agent is stuck).
- [ ] **Reporting**:
    - [ ] Implement daily/weekly reporting logic using data from MongoDB/Supabase.

## 5. Deployment

- [ ] **Deploy Backend**: Prepare the agent for deployment (e.g., to a cloud provider or alongside the Netlify frontend via Functions if suitable, otherwise a standalone server).

### To-dos

- [ ] Clone unique-staffing-prof repository
- [ ] Initialize JobPal backend service
- [ ] Configure Supabase and MongoDB connections
- [ ] Implement OpenAI Agent for HR tasks
- [ ] Integrate ElevenLabs for voice