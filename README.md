TeamPulse - Your operations, summarized daily without spreadsheets.

TeamPulse is an AI-powered back-office assistant that reads daily business data, monitors operations, generates intelligent reports, answers natural-language questions, and proactively flags issues before they escalate.

Problem Statement
Small and mid-sized businesses struggle with fragmented operational data across sales, attendance, tasks, and payments. Admin managers spend excessive time manually checking records, compiling reports, and chasing issues instead of making strategic decisions.
TeamPulse solves this by automating operational visibility and turning raw data into actionable insights—instantly.

 Key Features

 AI-Powered Q&A
Ask questions in plain English:

"Who didn't come to work yesterday?"
"Which clients haven't paid this month?"
"Summarize this week's sales activity"
"Show me Sarah's performance trends"

 Automated Reports

Daily Reports: Attendance summaries, sales snapshots, task completion status
Weekly Reports: Staff performance overviews, revenue trends, operational highlights
Custom Reports: Build your own with drag-and-drop interface

 Proactive Alerts
Get notified when something needs attention:

Low attendance trends
Repeated lateness patterns
Unpaid invoices past due date
Unusual drops in sales or productivity

 Staff Performance Summaries
Auto-generated weekly performance reports for each team member:

Attendance consistency
Task completion rates
Sales contributions
Performance tags and recommendations

 Data Integration
Connect multiple data sources:

CSV/Excel uploads
Google Sheets (real-time sync)
Manual entry
API integrations (Salesforce, BambooHR, Asana)


 Quick Start
Prerequisites

Python 3.11+
Node.js 20+
PostgreSQL 15+
Redis 7+
Docker (optional, for containerized setup)

Installation

1. Clone the Repository
bashgit clone https://github.com/your-org/teampulse.git
cd teampulse
2. Backend Setup
bashcd backend

 Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

 Install dependencies
pip install -r requirements.txt

Copy environment variables
cp .env.example .env

 Edit .env with your credentials:
 - DATABASE_URL
# - REDIS_URL
# - CLAUDE_API_KEY
# - SECRET_KEY

# Run database migrations
alembic upgrade head

 Start the API server
uvicorn src.main:app --reload --port 8000
3. Frontend Setup
bashcd ../frontend

 Install dependencies
npm install

 Copy environment variables
cp .env.example .env.local

 Edit .env.local with your API URL
 NEXT_PUBLIC_API_URL=http://localhost:8000

# Start development server
npm run dev
4. Access the Application
Open your browser and navigate to:

Frontend: http://localhost:3000
API Docs: http://localhost:8000/docs

Docker Setup (Alternative)
bash# Build and run all services
docker-compose up -d

 View logs
docker-compose logs -f

 Stop all services
docker-compose down



 Architecture Overview
┌─────────────────────────────────────────────────────────┐
│                    Client Layer                          │
│     Web App (React) | Mobile Apps (React Native)        │
└────────────────────────┬────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────┐
│                  API Gateway (Kong)                      │
│    Authentication | Rate Limiting | Request Routing     │
└────────────────────────┬────────────────────────────────┘
                         │
        ┌────────────────┼────────────────┐
        │                │                │
        ▼                ▼                ▼
┌──────────────┐  ┌──────────────┐  ┌──────────────┐
│    Query     │  │    Report    │  │    Alert     │
│   Service    │  │   Service    │  │   Service    │
└──────┬───────┘  └──────┬───────┘  └──────┬───────┘
       │                 │                 │
       └─────────────────┼─────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────┐
│              AI Orchestration Layer                      │
│    Claude API | Context Management | ML Models          │
└────────────────────────┬────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────┐
│                   Data Layer                             │
│   PostgreSQL | Redis | Vector DB | S3                   │
└─────────────────────────────────────────────────────────┘
For detailed architecture documentation, see docs/architecture.md.

 Configuration
Environment Variables
Backend (.env)
bash# Database
DATABASE_URL=postgresql://user:password@localhost:5432/teampulse
REDIS_URL=redis://localhost:6379/0

 AI Services
CLAUDE_API_KEY=your_anthropic_api_key_here

 Security
SECRET_KEY=your-secret-key-here
JWT_ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=30

 AWS (for S3 storage)
AWS_ACCESS_KEY_ID=your_aws_key
AWS_SECRET_ACCESS_KEY=your_aws_secret
AWS_S3_BUCKET=teampulse-reports

 Email (for notifications)
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your_email@gmail.com
SMTP_PASSWORD=your_app_password

 Application
ENVIRONMENT=development
DEBUG=true
CORS_ORIGINS=http://localhost:3000
Frontend (.env.local)
bashNEXT_PUBLIC_API_URL=http://localhost:8000
NEXT_PUBLIC_WS_URL=ws://localhost:8000
NEXT_PUBLIC_APP_NAME=TeamPulse

Usage Examples
1. Upload Data
bash Upload CSV file via API
curl -X POST "http://localhost:8000/api/v1/data/upload" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -F "file=@attendance.csv" \
  -F "data_type=attendance"
2. Ask a Question
pythonimport requests

response = requests.post(
    "http://localhost:8000/api/v1/query",
    headers={"Authorization": "Bearer YOUR_TOKEN"},
    json={
        "query": "Who didn't come to work yesterday?",
        "workspace_id": "ws_123"
    }
)

print(response.json())
 Output:
 {
   "answer": "Yesterday (Dec 15), 2 employees were absent:\n• John Doe (E123)\n• Jane Smith (E456)",
   "data": [...],
   "confidence": 0.95
 }
3. Generate Report
pythonresponse = requests.post(
    "http://localhost:8000/api/v1/reports/generate",
    headers={"Authorization": "Bearer YOUR_TOKEN"},
    json={
        "report_type": "weekly_performance",
        "workspace_id": "ws_123",
        "date_range": {
            "start": "2025-12-09",
            "end": "2025-12-15"
        }
    }
)

report_url = response.json()["report_url"]
print(f"Report available at: {report_url}")
4. Set Up Alert
pythonresponse = requests.post(
    "http://localhost:8000/api/v1/alerts/rules",
    headers={"Authorization": "Bearer YOUR_TOKEN"},
    json={
        "name": "High Absence Alert",
        "condition": {
            "metric": "daily_absence_count",
            "operator": "greater_than",
            "threshold": 5
        },
        "notification_channels": ["email", "slack"],
        "workspace_id": "ws_123"
    }
)

 Testing
Run All Tests
bash# Backend tests
cd backend
pytest

 Frontend tests
cd frontend
npm test

 Integration tests
cd backend
pytest tests/integration/

 E2E tests
cd frontend
npm run test:e2e
Test Coverage
bash# Generate coverage report
cd backend
pytest --cov=src --cov-report=html

 View coverage
open htmlcov/index.html

 API Documentation
Interactive API Docs
Once the backend is running, visit:

Swagger UI: http://localhost:8000/docs
ReDoc: http://localhost:8000/redoc

Key Endpoints
MethodEndpointDescriptionPOST/api/v1/querySubmit natural language queryGET/api/v1/reportsList available reportsPOST/api/v1/reports/generateGenerate on-demand reportGET/api/v1/alertsList active alertsPOST/api/v1/alerts/rulesCreate alert rulePOST/api/v1/data/uploadUpload data fileGET/api/v1/integrationsList connected integrations
For complete API documentation, see docs/api.md.

 Security
Authentication
TeamPulse uses JWT-based authentication:
bash# Login
curl -X POST "http://localhost:8000/api/v1/auth/login" \
  -H "Content-Type: application/json" \
  -d '{"email":"admin@example.com","password":"secure_password"}'

 Returns:
 {
   "access_token": "eyJ0eXAiOiJKV1QiLCJhbGc...",
   "token_type": "bearer"
 }

 Use token in subsequent requests
curl -X GET "http://localhost:8000/api/v1/dashboard" \
  -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGc..."
Role-Based Access Control (RBAC)

Admin: Full access to all features
Manager: Access to team data and reports
Viewer: Read-only access to reports
Contributor: Can input data but limited report access

Data Security

 Encryption at rest: AES-256 for database and storage
 Encryption in transit: TLS 1.3 for all connections
 SQL Injection Protection: Parameterized queries only
 Secrets Management: AWS Secrets Manager integration
 Audit Logging: Complete audit trail of all actions


 Deployment
Production Deployment (Kubernetes)
bash# Apply Kubernetes manifests
kubectl apply -f k8s/

 Or use Helm
helm install teampulse ./helm/teampulse \
  --set image.tag=v1.0.0 \
  --set database.host=your-db-host
Docker Compose (Simple Deployment)
bash# Production build
docker-compose -f docker-compose.prod.yml up -d

 Scale services
docker-compose -f docker-compose.prod.yml up -d --scale api=3
For detailed deployment instructions, see docs/deployment.md.

 Monitoring
Health Checks
bash# API health
curl http://localhost:8000/health

 Database health
curl http://localhost:8000/health/db

 Redis health
curl http://localhost:8000/health/redis
Metrics
TeamPulse exposes Prometheus metrics at /metrics:
# Query metrics
teampulse_queries_total{workspace_id="ws_123"} 1250
teampulse_query_duration_seconds{quantile="0.95"} 2.3

 AI metrics
teampulse_claude_api_calls_total{operation="query"} 845
teampulse_claude_api_errors_total 12

 Business metrics
teampulse_active_workspaces 42
teampulse_daily_active_users 156

 Contributing
We welcome contributions! Please see CONTRIBUTING.md for details.
Development Workflow

Fork the repository
Create a feature branch: git checkout -b feature/amazing-feature
Make your changes
Add tests for new functionality
Run tests: pytest and npm test
Commit: git commit -m 'Add amazing feature'
Push: git push origin feature/amazing-feature
Open a Pull Request

Code Style

Python: Follow PEP 8, use black for formatting
TypeScript: Follow Airbnb style guide, use prettier
Commits: Follow Conventional Commits

bash: Format code
cd backend && black src/
cd frontend && npm run format

 Lint code
cd backend && flake8 src/
cd frontend && npm run lint

 Roadmap

 MVP (Completed)

 CSV data upload
 AI-powered Q&A
 Daily & weekly reports
 Performance summaries
 Basic alerts
 Web dashboard

 Phase 2 (In Progress)

 Google Sheets real-time sync
 Slack integration
 Custom report builder
 Mobile apps (iOS/Android)
 Email notifications

 Phase 3 (Planned)

 WhatsApp integration
 Voice-based queries
 Predictive insights
 Multi-branch analytics
 Custom KPI dashboards
 Advanced integrations (Salesforce, BambooHR)

See our public roadmap for more details.

 License
This project is licensed under the MIT License - see the LICENSE file for details.

Acknowledgments

Anthropic for Claude AI API
FastAPI for the excellent Python web framework
React and Next.js teams for frontend tools
All our contributors
