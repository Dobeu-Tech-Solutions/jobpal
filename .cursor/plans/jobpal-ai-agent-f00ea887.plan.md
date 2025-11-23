<!-- f00ea887-dced-437b-880a-eb095c095c50 a35a6559-159b-4b66-b264-14fadba5dfe7 -->
# JobPal AI Agent Development Plan

## Architecture Overview

Build a containerized microservices architecture with the following components:

- **API Gateway**: Main entry point for all services
- **Core Services**: Authentication, Data Migration, Form Processing, Communication
- **AI Services**: Voice Agent (ElevenLabs), Natural Language Processing
- **Database Layer**: MongoDB (primary), Supabase (initial data source)
- **Message Queue**: For async processing and workflow orchestration

## Project Structure

```
jobpal/
├── docker-compose.yml
├── services/
│   ├── api-gateway/
│   ├── auth-service/
│   ├── data-migration/
│   ├── form-processor/
│   ├── communication/
│   ├── voice-agent/
│   ├── workflow-engine/
│   └── reporting/
├── shared/
│   ├── database/
│   ├── utils/
│   └── types/
└── infrastructure/
    ├── mongodb/
    └── redis/
```

## Key Components

### 1. Data Migration Service

- Connect to Supabase using provided credentials
- Set up MongoDB with x.509 certificate authentication
- Create migration pipeline from Supabase to MongoDB
- Implement real-time sync for ongoing data updates
- Files: `services/data-migration/`

### 2. Form Processing Service

- PDF extraction using pdf-parse or PyPDF2
- OCR capabilities for image-based forms
- Field validation and mapping to MongoDB schema
- Photo upload handling and storage
- Files: `services/form-processor/`

### 3. Communication Service

- Brevo integration for SMS (will set up account)
- Multilingual message templates (Spanish, French, English, Ethiopian)
- Twilio fallback for phone calls
- Message scheduling and tracking
- Files: `services/communication/`

### 4. Voice Agent Service

- ElevenLabs integration for voice synthesis
- Conversational AI using OpenAI GPT-4
- Call routing and handling
- Voice-to-text transcription
- Files: `services/voice-agent/`

### 5. Workflow Engine

- 60-day onboarding workflow automation
- Task scheduling and reminders
- Human-in-the-loop checkpoints
- Status tracking and notifications
- Files: `services/workflow-engine/`

### 6. Reporting Service

- Daily/weekly candidate reports
- Dashboard for monitoring
- Export capabilities (PDF, CSV)
- Files: `services/reporting/`

## Technology Stack

- **Backend**: Node.js with TypeScript
- **Framework**: Express.js / Fastify
- **Database**: MongoDB (primary), Redis (caching)
- **Message Queue**: Bull/BullMQ
- **AI/ML**: OpenAI API, ElevenLabs API
- **Container**: Docker, Docker Compose
- **Monitoring**: Prometheus, Grafana

## Security Considerations

- X.509 certificate authentication for MongoDB
- JWT-based API authentication
- Encrypted data at rest and in transit
- GDPR/privacy compliance for employee data
- Role-based access control (RBAC)

## Integration Points

- **Supabase**: Initial data source
- **MongoDB**: Primary database
- **ElevenLabs**: Voice synthesis
- **Brevo**: SMS messaging
- **Adobe APIs**: Form processing (optional)
- **Netlify**: Frontend integration

### To-dos

- [ ] Set up development environment with Docker and necessary tools
- [ ] Configure MongoDB connection with x.509 certificate
- [ ] Establish Supabase connection and explore schema
- [ ] Build data migration service from Supabase to MongoDB
- [ ] Create Brevo account and configure SMS capabilities
- [ ] Implement PDF form extraction and validation service
- [ ] Develop ElevenLabs voice agent with conversational AI
- [ ] Build workflow orchestration for 60-day onboarding
- [ ] Create multilingual SMS/call communication service
- [ ] Implement reporting service with daily/weekly reports
- [ ] Dockerize all microservices and create docker-compose
- [ ] Test end-to-end workflow with sample data
- [ ] Deploy to staging environment for UAT