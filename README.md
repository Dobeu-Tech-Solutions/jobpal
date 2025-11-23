# JobPal - AI-Powered HR Onboarding Agent

An intelligent HR onboarding and compliance coordination system for Unique Staffing Professionals Inc., automating employee onboarding workflows with multilingual support and human-in-the-loop oversight.

## Overview

JobPal is an AI agent that handles the complete employee onboarding process after recruiters receive verbal commitment from candidates. The system manages paperwork completion, compliance verification, multilingual communication, and provides 24/7 support during the critical first 60-90 days of employment.

### Key Features

- **Automated Data Migration**: Seamless transfer from Supabase (web forms) to MongoDB (secure storage)
- **Intelligent Form Processing**: AI-powered PDF form completion and validation
- **Multilingual Support**: Spanish, French, English, Amharic, and more
- **24/7 Communication**: SMS and voice support via ElevenLabs AI
- **Human-in-the-Loop**: Escalation workflows for complex cases
- **Compliance Tracking**: Automated verification of required documentation
- **Real-time Reporting**: Daily and weekly candidate status reports

## Architecture

JobPal uses a microservices architecture with the following components:

```
┌─────────────────────────────────────────────────────────────┐
│                        API Gateway                           │
└─────────────────────────────────────────────────────────────┘
                              │
        ┌─────────────────────┼─────────────────────┐
        │                     │                     │
┌───────▼────────┐   ┌────────▼────────┐   ┌──────▼──────┐
│ Auth Service   │   │ Data Migration  │   │ Form        │
│                │   │ Service         │   │ Processor   │
└────────────────┘   └─────────────────┘   └─────────────┘
        │                     │                     │
┌───────▼────────┐   ┌────────▼────────┐   ┌──────▼──────┐
│ Communication  │   │ Voice Agent     │   │ Workflow    │
│ Service        │   │ (ElevenLabs)    │   │ Engine      │
└────────────────┘   └─────────────────┘   └─────────────┘
        │                     │                     │
        └─────────────────────┼─────────────────────┘
                              │
                    ┌─────────▼─────────┐
                    │   Reporting       │
                    │   Service         │
                    └───────────────────┘
                              │
        ┌─────────────────────┼─────────────────────┐
        │                     │                     │
┌───────▼────────┐   ┌────────▼────────┐   ┌──────▼──────┐
│   Supabase     │   │    MongoDB      │   │   Redis     │
│   (Initial)    │   │   (Primary)     │   │   (Queue)   │
└────────────────┘   └─────────────────┘   └─────────────┘
```

## Tech Stack

### Backend
- **Runtime**: Node.js 20+ / TypeScript 5+
- **Framework**: Express.js / Fastify
- **API**: RESTful + GraphQL (optional)
- **Message Queue**: Redis / Bull

### Databases
- **MongoDB Atlas**: Primary secure employee data storage
- **Supabase**: Initial data collection from web forms
- **Redis**: Caching and job queues

### AI Services
- **OpenAI GPT-4**: Natural language processing, form understanding
- **ElevenLabs**: Voice synthesis and conversational AI
- **OpenAI Vision**: PDF form extraction and validation

### Infrastructure
- **Containerization**: Docker + Docker Compose
- **Orchestration**: Kubernetes (production)
- **CI/CD**: GitHub Actions
- **Monitoring**: Prometheus + Grafana

## Prerequisites

- Node.js 20+ and npm/yarn
- Docker and Docker Compose
- MongoDB Atlas account (credentials provided)
- Supabase account (credentials provided)
- OpenAI API key
- ElevenLabs API key

## Quick Start

### 1. Clone the Repository

```bash
git clone https://github.com/Dobeu-Tech-Solutions/jobpal.git
cd jobpal
```

### 2. Environment Setup

Create a `.env` file in the root directory:

```bash
cp .env.example .env
```

Configure the following environment variables:

```env
# MongoDB Configuration
MONGODB_URI=mongodb+srv://cluster0.mongodb.net/uniquestaffingprofessionals
MONGODB_USER=jobpal-uniquestaff-dbe-user@$external
MONGODB_CERT_PATH=./X509-cert-5832369353354208953.pem
MONGODB_PROJECT_ID=691ed22b4827ea3ad4f64b9d

# Supabase Configuration
SUPABASE_URL=https://your-project.supabase.co
SUPABASE_KEY=your-supabase-anon-key
SUPABASE_EMAIL=jeremyw@uniquestaffingprofessionals.com
SUPABASE_PASSWORD=Norrisandgold1!

# OpenAI Configuration
OPENAI_API_KEY=your-openai-api-key
OPENAI_MODEL=gpt-4-turbo-preview

# ElevenLabs Configuration
ELEVENLABS_API_KEY=your-elevenlabs-api-key
ELEVENLABS_VOICE_ID=your-voice-id

# Redis Configuration
REDIS_URL=redis://localhost:6379

# Application Configuration
NODE_ENV=development
PORT=3000
LOG_LEVEL=info
```

### 3. Install Dependencies

```bash
# Install root dependencies
npm install

# Install service dependencies
npm run install:all
```

### 4. Start Development Environment

Using Docker Compose:

```bash
docker-compose up -d
```

Or run services individually:

```bash
# Start databases
npm run dev:db

# Start API Gateway
npm run dev:gateway

# Start all services
npm run dev:services
```

### 5. Verify Installation

```bash
# Check service health
curl http://localhost:3000/health

# Run tests
npm test
```

## Project Structure

```
jobpal/
├── docs/                          # Documentation
│   ├── architecture.md            # Architecture details
│   ├── api/                       # API documentation
│   ├── database/                  # Database schemas
│   └── deployment.md              # Deployment guide
├── services/                      # Microservices
│   ├── api-gateway/              # Main API entry point
│   ├── auth-service/             # Authentication & authorization
│   ├── data-migration/           # Supabase to MongoDB migration
│   ├── form-processor/           # PDF form processing
│   ├── communication/            # SMS/Email service
│   ├── voice-agent/              # ElevenLabs integration
│   ├── workflow-engine/          # Orchestration logic
│   └── reporting/                # Analytics and reports
├── shared/                        # Shared code
│   ├── database/                 # Database clients
│   ├── utils/                    # Utility functions
│   ├── types/                    # TypeScript types
│   └── middleware/               # Shared middleware
├── infrastructure/                # Infrastructure as code
│   ├── docker/                   # Dockerfiles
│   ├── kubernetes/               # K8s manifests
│   └── terraform/                # Cloud infrastructure
├── tests/                         # Test suites
│   ├── unit/                     # Unit tests
│   ├── integration/              # Integration tests
│   └── e2e/                      # End-to-end tests
├── scripts/                       # Utility scripts
│   ├── migrate.ts                # Database migration
│   ├── seed.ts                   # Seed data
│   └── deploy.sh                 # Deployment script
├── .devcontainer/                # Dev container config
├── docker-compose.yml            # Local development
├── package.json                  # Root dependencies
└── README.md                     # This file
```

## Development Workflow

### Running Services Locally

```bash
# Start specific service
npm run dev --workspace=services/api-gateway

# Start all services
npm run dev:all

# Watch mode with hot reload
npm run dev:watch
```

### Database Operations

```bash
# Run migrations
npm run migrate

# Seed development data
npm run seed

# Backup database
npm run db:backup

# Restore database
npm run db:restore
```

### Testing

```bash
# Run all tests
npm test

# Run unit tests
npm run test:unit

# Run integration tests
npm run test:integration

# Run with coverage
npm run test:coverage

# Run specific service tests
npm test --workspace=services/form-processor
```

### Code Quality

```bash
# Lint code
npm run lint

# Format code
npm run format

# Type check
npm run type-check

# Run all checks
npm run check
```

## API Documentation

API documentation is available at:
- **Development**: http://localhost:3000/api/docs
- **Swagger UI**: http://localhost:3000/api/swagger
- **GraphQL Playground**: http://localhost:3000/graphql

See [docs/api/README.md](docs/api/README.md) for detailed API documentation.

## Database Schemas

### MongoDB Collections

- `candidates`: Employee candidate information
- `forms`: Completed form data and status
- `tasks`: Onboarding task tracking
- `communications`: SMS/call logs
- `reports`: Generated reports
- `audit_logs`: System audit trail

See [docs/database/schemas.md](docs/database/schemas.md) for detailed schemas.

## Deployment

### Development
```bash
npm run deploy:dev
```

### Staging
```bash
npm run deploy:staging
```

### Production
```bash
npm run deploy:prod
```

See [docs/deployment.md](docs/deployment.md) for detailed deployment instructions.

## Monitoring and Logging

- **Logs**: Centralized logging with Winston
- **Metrics**: Prometheus metrics at `/metrics`
- **Tracing**: OpenTelemetry integration
- **Health Checks**: `/health` and `/ready` endpoints

## Security

- **Authentication**: JWT-based authentication
- **Authorization**: Role-based access control (RBAC)
- **Encryption**: TLS/SSL for all communications
- **Secrets**: Environment variables and secret management
- **Compliance**: GDPR and data privacy compliant

## Contributing

1. Create a feature branch: `git checkout -b feature/your-feature`
2. Make your changes and commit: `git commit -m "Add feature"`
3. Push to the branch: `git push origin feature/your-feature`
4. Create a Pull Request

### Commit Convention

Follow conventional commits:
```
feat: Add new feature
fix: Fix bug
docs: Update documentation
test: Add tests
refactor: Refactor code
chore: Update dependencies
```

## Troubleshooting

### Common Issues

**MongoDB Connection Failed**
```bash
# Verify certificate permissions
chmod 600 X509-cert-5832369353354208953.pem

# Test connection
npm run test:mongodb
```

**Supabase Authentication Failed**
```bash
# Verify credentials in .env
# Check Supabase project status
npm run test:supabase
```

**Service Won't Start**
```bash
# Check logs
docker-compose logs -f service-name

# Restart service
docker-compose restart service-name
```

## Support

- **Documentation**: [docs/](docs/)
- **Issues**: [GitHub Issues](https://github.com/Dobeu-Tech-Solutions/jobpal/issues)
- **Email**: support@uniquestaffingprofessionals.com

## License

Proprietary - Unique Staffing Professionals Inc.

## Related Projects

- **Frontend**: [unique-staffing-prof](https://github.com/Dobeu-Tech-Solutions/unique-staffing-prof)
- **Production Site**: https://uniquestaffingprofessionals.com
- **Development Site**: https://unique-staffing-professionals.netlify.app

---

**Built with ❤️ by Dobeu Tech Solutions**
