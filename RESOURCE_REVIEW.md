# Resource Review: JobPal AI Agent Integration Tools

## Overview
This document reviews available tools, resources, and integration capabilities for the JobPal AI Agent project, focusing on Supabase, MongoDB Atlas/Mongoose, OpenAI Agents/ChatKit/Agent Builder, and Eleven Labs.

---

## 1. Supabase

### Available Resources & Tools

#### **Core SDK & Client Libraries**
- **JavaScript/TypeScript Client**: `@supabase/supabase-js`
  - Official NPM package for browser and Node.js
  - Supports real-time subscriptions, authentication, storage, and database operations
  - Documentation: https://supabase.com/docs/reference/javascript/introduction

#### **Key Features for JobPal**
- **Real-time Subscriptions**: Listen to database changes (perfect for webhook triggers)
- **Webhooks**: Database webhooks for insert/update events
- **Edge Functions**: Serverless functions for migration scripts
- **Row Level Security (RLS)**: Secure employee data access
- **Storage**: Handle PDF form uploads and photos

#### **Integration Points**
- **Database Webhooks**: Trigger migrations when new applicants are added
- **Real-time Sync**: Monitor Supabase changes and sync to MongoDB
- **Authentication**: Verify email/text verification status
- **Storage API**: Upload and retrieve PDF forms and employee photos

#### **Project-Specific Configuration**
- **Project ID**: `691ed22b4827ea3ad4f64b9d`
- **Credentials**: Username: `jeremyw@uniquestaffingprofessionals.com`
- **Use Case**: Initial data capture from "Join our network" form

#### **Recommended Implementation**
```typescript
// supabaseClient.ts
import { createClient } from '@supabase/supabase-js'

const supabaseUrl = process.env.SUPABASE_URL
const supabaseServiceKey = process.env.SUPABASE_SERVICE_ROLE_KEY

export const supabase = createClient(supabaseUrl, supabaseServiceKey, {
  auth: {
    autoRefreshToken: false,
    persistSession: false
  }
})
```

---

## 2. MongoDB Atlas & Mongoose

### Available Resources & Tools

#### **MongoDB Atlas**
- **Official Documentation**: https://www.mongodb.com/docs/atlas/
- **Node.js Driver**: `mongodb` package
- **Mongoose ODM**: `mongoose` package (recommended for schema management)

#### **Authentication Method**
- **X.509 Certificate Authentication**
  - Username: `jobpal-uniquestaff-dbe-user@$external`
  - Requires certificate file for connection
  - More secure than username/password

#### **Key Features for JobPal**
- **Vector Search**: Store embeddings for AI agent memory (future enhancement)
- **Atlas Search**: Full-text search capabilities
- **Change Streams**: Real-time database change notifications
- **Data Lake**: Analytics and reporting capabilities
- **Replica Sets**: High availability

#### **Mongoose Benefits**
- **Schema Validation**: Enforce data structure for employee records
- **Middleware**: Pre/post hooks for data transformation
- **Model Methods**: Custom business logic encapsulation
- **Population**: Easy relationship handling

#### **Recommended Implementation**
```typescript
// mongoClient.ts
import mongoose from 'mongoose'
import fs from 'fs'

const mongoUri = process.env.MONGODB_URI
const certPath = process.env.MONGODB_CERT_PATH

const options = {
  ssl: true,
  sslCert: fs.readFileSync(certPath),
  sslKey: fs.readFileSync(certPath),
  authMechanism: 'MONGODB-X509',
  authSource: '$external'
}

export const connectMongoDB = async () => {
  await mongoose.connect(mongoUri, options)
  console.log('MongoDB Atlas connected')
}
```

#### **Schema Example**
```typescript
// models/Employee.ts
import mongoose from 'mongoose'

const EmployeeSchema = new mongoose.Schema({
  supabaseId: { type: String, unique: true },
  email: String,
  phone: String,
  formsCompleted: Boolean,
  verificationStatus: {
    email: Boolean,
    text: Boolean
  },
  onboardingStartDate: Date,
  migrationDate: Date,
  // ... other fields
})

export const Employee = mongoose.model('Employee', EmployeeSchema)
```

---

## 3. OpenAI Tools

### 3.1 OpenAI Agents Realtime (`@openai/agents-realtime`)

#### **Purpose**
- Real-time voice agent framework for browser-based voice interactions
- Supports tool delegation and multi-turn conversations
- Designed for voice-first applications

#### **Key Features**
- **RealtimeSession**: Browser-based voice session management
- **Tool Calling**: Agent can call custom tools (Supabase, MongoDB, SMS)
- **Streaming**: Real-time audio and text streaming
- **Multilingual**: Supports multiple languages (English, Spanish, French, Amharic)

#### **Integration Pattern**
```typescript
// backend/api/token.ts
import { generateRealtimeToken } from '@openai/agents-realtime'

export async function POST(req: Request) {
  const token = await generateRealtimeToken({
    model: 'gpt-4o-realtime-preview',
    tools: [supabaseTool, mongoTool, smsTool],
    instructions: 'You are an HR onboarding coordinator...'
  })
  return Response.json({ token })
}
```

#### **Frontend Integration**
```typescript
// voice-agent/src/App.tsx
import { RealtimeSession } from '@openai/agents-realtime'

const session = new RealtimeSession({
  token: await fetchToken(),
  onTranscript: (text) => updateTranscript(text),
  onAudio: (audio) => playAudio(audio)
})
```

### 3.2 OpenAI ChatGPT ChatKit (`@openai/chatkit-js`)

#### **Purpose**
- Framework for building AI-powered chat interfaces
- UI components and utilities for chat experiences
- Supports streaming, tool integration, and custom widgets

#### **Key Features**
- **Deep UI Customization**: Customizable chat components
- **Response Streaming**: Real-time message rendering
- **Tool Integration**: Built-in support for function calling
- **Interactive Widgets**: Rich media support

#### **Use Case for JobPal**
- Alternative to RealtimeSession for text-based interactions
- Could power SMS-based chat interface
- Dashboard chat interface for HR coordinators

#### **Documentation**
- GitHub: https://github.com/openai/chatkit-js
- Docs: https://openai.github.io/chatkit-js/

### 3.3 OpenAI Agent Builder

#### **Purpose**
- Visual no-code tool for building AI agents
- Multi-agent workflow orchestration
- Testing and deployment tools

#### **Key Features**
- **Visual Canvas**: Drag-and-drop agent building
- **Multi-Agent Workflows**: Coordinate multiple agents
- **Evaluation Suite**: Test agent performance
- **Deployment**: One-click deployment options

#### **Use Case for JobPal**
- Rapid prototyping of onboarding workflows
- Testing different agent configurations
- Visualizing agent decision trees

#### **Resources**
- Official documentation: https://platform.openai.com/docs/guides/agents
- Agent Builder: Visual tool for agent creation

---

## 4. Eleven Labs

### Available Resources & Tools

#### **Text-to-Speech (TTS)**
- **Flash v2.5**: Fast, high-quality TTS (recommended for output)
- **API Endpoint**: `/v1/text-to-speech/{voice_id}`
- **Features**: Voice cloning, multilingual support, emotion control

#### **Speech-to-Text (STT)**
- **Scribe v1**: Accurate transcription (recommended for input)
- **API Endpoint**: `/v1/speech-to-text`
- **Features**: Real-time transcription, multilingual support

#### **Conversational AI**
- **Voice Agents**: Full duplex voice conversations
- **Integration**: Can connect to Supabase for real-time data access
- **Features**: Natural interruptions, context awareness

#### **Key Features for JobPal**
- **Multilingual Support**: Spanish, French, English, Amharic
- **Real-time Processing**: Low latency for natural conversations
- **Voice Cloning**: Consistent brand voice
- **Emotion Control**: Appropriate tone for HR interactions

#### **Recommended Implementation**
```typescript
// services/elevenLabs.ts
import { ElevenLabsClient } from 'elevenlabs'

const client = new ElevenLabsClient({
  apiKey: process.env.ELEVENLABS_API_KEY
})

export async function textToSpeech(text: string, voiceId: string) {
  const audio = await client.textToSpeech.convert(voiceId, {
    text,
    model_id: 'eleven_flash_v2_5',
    voice_settings: {
      stability: 0.5,
      similarity_boost: 0.75
    }
  })
  return audio
}

export async function speechToText(audioBuffer: Buffer) {
  const transcript = await client.speechToText.convert({
    audio: audioBuffer,
    model_id: 'eleven_scribe_v1'
  })
  return transcript
}
```

#### **Integration with Supabase**
- Eleven Labs agents can directly query Supabase databases
- Real-time data access during voice conversations
- Secure API integration via Supabase Edge Functions

---

## 5. Integration Architecture Recommendations

### **Data Flow**
```
Netlify Form → Supabase → Webhook → Backend API → MongoDB Atlas
                                    ↓
                            OpenAI Realtime Agent
                                    ↓
                            Eleven Labs TTS/STT
                                    ↓
                            Voice/SMS Interface
```

### **Recommended Tech Stack**

#### **Backend**
- **Runtime**: Node.js with TypeScript
- **Framework**: Express.js or Next.js API routes
- **Database Clients**: 
  - `@supabase/supabase-js` for Supabase
  - `mongoose` for MongoDB Atlas
- **AI**: `@openai/agents-realtime` for voice agent
- **Voice**: `elevenlabs` SDK

#### **Frontend**
- **Framework**: Vite + TypeScript
- **Voice Agent**: `@openai/agents-realtime` RealtimeSession
- **UI**: React or Vue.js

#### **Migration Scripts**
- **Language**: TypeScript/Node.js
- **Scheduling**: Node-cron or GitHub Actions
- **Error Handling**: Retry logic with exponential backoff

### **Key Integration Points**

1. **Supabase Webhook → Migration**
   - Listen for new applicant inserts
   - Trigger migration to MongoDB
   - Update verification status

2. **OpenAI Agent Tools**
   - `SupabaseTool`: Query applicant data
   - `MongoTool`: Update employee records
   - `SMSNotifyTool`: Send multilingual SMS
   - `FormValidationTool`: Check PDF completion

3. **Eleven Labs Integration**
   - TTS for agent responses
   - STT for user voice input
   - Multilingual voice models

4. **Reporting**
   - Daily cron job querying MongoDB
   - Aggregate candidate interactions
   - Email reports or store in MongoDB

---

## 6. Security Considerations

### **Supabase**
- Use service role key only on backend
- Implement Row Level Security (RLS) policies
- Secure webhook endpoints with signatures

### **MongoDB Atlas**
- X.509 certificate authentication (already configured)
- Network IP whitelisting
- Encryption at rest and in transit

### **OpenAI**
- Store API keys in environment variables
- Use ephemeral tokens for RealtimeSession
- Implement rate limiting

### **Eleven Labs**
- Secure API key storage
- Monitor usage and costs
- Implement voice authentication for sensitive operations

---

## 7. Documentation Links

### **Supabase**
- Main Docs: https://supabase.com/docs
- JavaScript Client: https://supabase.com/docs/reference/javascript/introduction
- Webhooks: https://supabase.com/docs/guides/database/webhooks
- Real-time: https://supabase.com/docs/guides/realtime

### **MongoDB Atlas**
- Atlas Docs: https://www.mongodb.com/docs/atlas/
- Node.js Driver: https://www.mongodb.com/docs/drivers/node/current/
- Mongoose: https://mongoosejs.com/docs/
- X.509 Auth: https://www.mongodb.com/docs/manual/core/security-x.509/

### **OpenAI**
- Agents Realtime: https://github.com/openai/agents-js (check for realtime package)
- ChatKit: https://openai.github.io/chatkit-js/
- API Docs: https://platform.openai.com/docs
- Agent Builder: https://platform.openai.com/docs/guides/agents

### **Eleven Labs**
- Main Docs: https://docs.elevenlabs.io/
- TTS API: https://docs.elevenlabs.io/api-reference/text-to-speech
- STT API: https://docs.elevenlabs.io/api-reference/speech-to-text
- Voice Agents: https://docs.elevenlabs.io/voice-agents
- Supabase Integration: https://elevenlabs.io/agents/integrations/supabase

---

## 8. Next Steps

1. **Set up environment variables** for all services
2. **Create client initialization files** (supabaseClient.ts, mongoClient.ts)
3. **Implement migration script** (migrateSupabaseToMongo.ts)
4. **Build backend API endpoints** for token generation and webhooks
5. **Create voice agent frontend** with RealtimeSession
6. **Define agent tools** (Supabase, MongoDB, SMS)
7. **Integrate Eleven Labs** TTS/STT
8. **Set up reporting** cron jobs
9. **Test end-to-end** workflow

---

## 9. MCP Server Considerations

While MCP resources are not currently available, the project plans mention using MCP servers for:
- `SupabaseTool`: Query and update Supabase data
- `MongoTool`: MongoDB operations
- `SMSNotifyTool`: Twilio/Brevo SMS integration

**Alternative Approach**: Implement these as custom functions/tools that the OpenAI Realtime Agent can call directly, rather than relying on MCP servers.

---

*Last Updated: Based on current project requirements and available tooling*
