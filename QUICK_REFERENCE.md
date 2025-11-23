# Quick Reference: Package Installation & Setup

## Required NPM Packages

### Core Dependencies

```bash
# Supabase
npm install @supabase/supabase-js

# MongoDB & Mongoose
npm install mongodb mongoose

# OpenAI Agents
npm install @openai/agents-realtime
# OR for ChatKit
npm install @openai/chatkit-js

# Eleven Labs
npm install elevenlabs

# Backend Framework (choose one)
npm install express
# OR
npm install next

# TypeScript
npm install -D typescript @types/node @types/express
```

### Development Dependencies

```bash
# Build Tools
npm install -D vite @vitejs/plugin-react
npm install -D tsx nodemon

# Environment Variables
npm install dotenv

# Scheduling
npm install node-cron

# HTTP Client
npm install axios

# PDF Processing (for form validation)
npm install pdf-parse
```

---

## Environment Variables Template

Create a `.env` file with:

```env
# Supabase
SUPABASE_URL=https://691ed22b4827ea3ad4f64b9d.supabase.co
SUPABASE_SERVICE_ROLE_KEY=your_service_role_key
SUPABASE_ANON_KEY=your_anon_key

# MongoDB Atlas
MONGODB_URI=mongodb+srv://cluster.mongodb.net/database?authSource=$external
MONGODB_CERT_PATH=./path/to/x509-cert.pem
MONGODB_DB_NAME=uniquestaffingprofessionals

# OpenAI
OPENAI_API_KEY=sk-...
OPENAI_ORG_ID=org-...

# Eleven Labs
ELEVENLABS_API_KEY=...

# Twilio (for SMS)
TWILIO_ACCOUNT_SID=...
TWILIO_AUTH_TOKEN=...
TWILIO_PHONE_NUMBER=...

# Server
PORT=3000
NODE_ENV=development
```

---

## Key API Endpoints to Implement

### Backend Endpoints

1. **Token Generation** (`POST /api/token`)
   - Generates ephemeral OpenAI Realtime token
   - Returns token for frontend use

2. **Supabase Webhook** (`POST /api/supabase-webhook`)
   - Receives Supabase database events
   - Triggers migration to MongoDB
   - Validates webhook signature

3. **Twilio Webhook** (`POST /api/twilio`)
   - Handles incoming SMS
   - Routes to agent or escalates to human
   - Sends multilingual responses

4. **Health Check** (`GET /api/health`)
   - Database connectivity check
   - Service status

---

## MongoDB Schema Example

```typescript
import mongoose from 'mongoose'

const EmployeeSchema = new mongoose.Schema({
  // Supabase reference
  supabaseId: {
    type: String,
    required: true,
    unique: true,
    index: true
  },
  
  // Contact info
  email: {
    type: String,
    required: true,
    lowercase: true,
    trim: true
  },
  phone: {
    type: String,
    required: true
  },
  
  // Verification status
  verification: {
    email: { type: Boolean, default: false },
    text: { type: Boolean, default: false },
    verifiedAt: Date
  },
  
  // Forms
  forms: {
    application: { type: Boolean, default: false },
    maryland2025: {
      completed: { type: Boolean, default: false },
      pdfUrl: String,
      photoUrl: String,
      completedAt: Date,
      errors: [String]
    }
  },
  
  // Onboarding
  onboarding: {
    startDate: Date,
    status: {
      type: String,
      enum: ['pending', 'in_progress', 'completed', 'on_hold'],
      default: 'pending'
    },
    currentStep: Number,
    daysRemaining: Number
  },
  
  // Migration tracking
  migration: {
    migratedAt: Date,
    migratedBy: String,
    source: { type: String, default: 'supabase' }
  },
  
  // Interaction logs
  interactions: [{
    type: { type: String, enum: ['call', 'sms', 'email'] },
    timestamp: { type: Date, default: Date.now },
    language: String,
    summary: String
  }],
  
  // Metadata
  createdAt: { type: Date, default: Date.now },
  updatedAt: { type: Date, default: Date.now }
}, {
  timestamps: true
})

// Indexes
EmployeeSchema.index({ email: 1 })
EmployeeSchema.index({ phone: 1 })
EmployeeSchema.index({ 'onboarding.status': 1 })
EmployeeSchema.index({ createdAt: -1 })

export const Employee = mongoose.model('Employee', EmployeeSchema)
```

---

## Supabase Table Structure (Expected)

Based on project requirements, Supabase should have:

```sql
-- Applicants table (from "Join our network" form)
CREATE TABLE applicants (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  email TEXT NOT NULL,
  phone TEXT,
  first_name TEXT,
  last_name TEXT,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Verification tracking
CREATE TABLE verifications (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  applicant_id UUID REFERENCES applicants(id),
  email_verified BOOLEAN DEFAULT FALSE,
  text_verified BOOLEAN DEFAULT FALSE,
  verified_at TIMESTAMP WITH TIME ZONE
);

-- Agent tasks (for tracking onboarding progress)
CREATE TABLE agent_tasks (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  applicant_id UUID REFERENCES applicants(id),
  task_type TEXT NOT NULL,
  status TEXT DEFAULT 'pending',
  completed_at TIMESTAMP WITH TIME ZONE,
  notes TEXT
);

-- Candidate status
CREATE TABLE candidate_status (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  applicant_id UUID REFERENCES applicants(id),
  status TEXT NOT NULL,
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);
```

---

## OpenAI Realtime Agent Tool Definitions

```typescript
// tools/supabaseTool.ts
export const supabaseTool = {
  type: 'function' as const,
  function: {
    name: 'query_supabase_applicant',
    description: 'Query applicant data from Supabase',
    parameters: {
      type: 'object',
      properties: {
        applicantId: {
          type: 'string',
          description: 'Supabase applicant ID'
        },
        email: {
          type: 'string',
          description: 'Applicant email address'
        }
      }
    }
  }
}

// tools/mongoTool.ts
export const mongoTool = {
  type: 'function' as const,
  function: {
    name: 'update_employee_record',
    description: 'Update employee record in MongoDB',
    parameters: {
      type: 'object',
      properties: {
        employeeId: { type: 'string' },
        updates: {
          type: 'object',
          description: 'Fields to update'
        }
      }
    }
  }
}

// tools/smsTool.ts
export const smsTool = {
  type: 'function' as const,
  function: {
    name: 'send_sms',
    description: 'Send SMS to employee in their preferred language',
    parameters: {
      type: 'object',
      properties: {
        phoneNumber: { type: 'string' },
        message: { type: 'string' },
        language: {
          type: 'string',
          enum: ['en', 'es', 'fr', 'am']
        }
      }
    }
  }
}
```

---

## Eleven Labs Voice Configuration

### Recommended Voice Models

```typescript
// voices.ts
export const VOICE_CONFIG = {
  english: {
    voiceId: 'your-english-voice-id',
    model: 'eleven_flash_v2_5',
    settings: {
      stability: 0.5,
      similarity_boost: 0.75,
      style: 0.0,
      use_speaker_boost: true
    }
  },
  spanish: {
    voiceId: 'your-spanish-voice-id',
    model: 'eleven_flash_v2_5',
    // ... similar settings
  },
  french: {
    voiceId: 'your-french-voice-id',
    model: 'eleven_flash_v2_5',
    // ... similar settings
  },
  amharic: {
    voiceId: 'your-amharic-voice-id',
    model: 'eleven_flash_v2_5',
    // ... similar settings
  }
}
```

### STT Configuration

```typescript
export const STT_CONFIG = {
  model: 'eleven_scribe_v1',
  language: 'auto', // or specific: 'en', 'es', 'fr', 'am'
  punctuate: true,
  format_text: true
}
```

---

## Migration Script Structure

```typescript
// scripts/migrateSupabaseToMongo.ts
import { supabase } from '../src/lib/supabaseClient'
import { Employee } from '../src/models/Employee'
import { connectMongoDB } from '../src/lib/mongoClient'

async function migrateApplicants() {
  await connectMongoDB()
  
  // Fetch applicants from Supabase
  const { data: applicants, error } = await supabase
    .from('applicants')
    .select('*')
    .is('migrated', null) // Only unmigrated
  
  if (error) throw error
  
  for (const applicant of applicants) {
    // Check if already exists
    const existing = await Employee.findOne({ 
      supabaseId: applicant.id 
    })
    
    if (existing) continue
    
    // Create MongoDB record
    const employee = new Employee({
      supabaseId: applicant.id,
      email: applicant.email,
      phone: applicant.phone,
      // ... map other fields
      migration: {
        migratedAt: new Date(),
        source: 'supabase'
      }
    })
    
    await employee.save()
    
    // Mark as migrated in Supabase (optional)
    await supabase
      .from('applicants')
      .update({ migrated: true })
      .eq('id', applicant.id)
  }
}

migrateApplicants().catch(console.error)
```

---

## Testing Checklist

- [ ] Supabase connection and query test
- [ ] MongoDB Atlas X.509 authentication test
- [ ] Migration script dry-run
- [ ] OpenAI Realtime token generation
- [ ] Eleven Labs TTS/STT test
- [ ] Webhook endpoint security validation
- [ ] Multilingual SMS sending
- [ ] PDF form parsing and validation
- [ ] End-to-end workflow test

---

*This is a living document - update as implementation progresses*
