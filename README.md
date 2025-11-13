# AutoCare System

A complete microservices-based system for an auto service center. The backend is built with ASP.NET Core (microservices) and PostgreSQL, and the frontend is a React application. The system includes user authentication, appointment management, real-time notifications via SignalR, and an AI-powered chatbot using Google Gemini.

Status: v1.2.0 — Core microservices, React frontend, SignalR real-time notifications, Google Gemini chatbot, Docker & Kubernetes-ready.

---

## Table of Contents

- Project Overview
- Features
- Architecture
- Services & Ports
- Tech Stack
- Prerequisites
- Repo layout
- Quickstart (Local)
  - Database
  - Backend services
  - Frontend (React)
- Environment configuration (examples)
- Docker Compose
- Kubernetes
- API documentation & Swagger
- SignalR (Realtime) integration
- Chatbot (Google Gemini) setup
- Testing
- CI / CD / Deployment notes
- Contributing
- Support
- License
- Changelog

---

## Project Overview

AutoCare System is a microservices backend + React frontend solution to manage vehicle service appointments, users, and real-time notifications. It’s designed for deployment in Docker or Kubernetes environments and integrates an AI chatbot powered by Google Gemini for natural language appointment inquiries.

---

## Features

- JWT authentication and role-based authorization (Customer, Employee, Admin)
- Appointment CRUD and status tracking (Pending, Approved, Completed, Cancelled)
- Vehicle & service type management
- Real-time notifications via SignalR
- AI chatbot using Google Gemini for conversational assistance and availability checking
- Shared PostgreSQL database (Shared Database Pattern)
- Docker and Kubernetes deployment support
- Swagger API docs for each service

---

## Architecture

- Microservices (each service is its own ASP.NET Core application)
- Shared PostgreSQL database (AutoService)
- Notification service listens for appointment changes (db events) and pushes SignalR messages
- Chatbot service uses Gemini API for NLP and references appointment data to respond contextually
- React SPA frontend consumes the backend APIs and connects to SignalR for real-time updates

---

## Services & Default Ports

- Authentication Service — http://localhost:5001
  - Endpoints: POST /api/Auth/register, POST /api/Auth/login, GET /api/Auth/profile
- Service Management Service — http://localhost:5002
  - Endpoints: GET /api/Appointments, POST /api/Appointments, PUT /api/Appointments/{id}, DELETE /api/Appointments/{id}
- Notification Service — http://localhost:5003
  - Endpoints: GET /api/Notifications, PUT /api/Notifications/{id}/read
  - SignalR Hub: /notificationHub (ws/wss)
- Chatbot Service — http://localhost:5004
  - Endpoints: POST /api/Chatbot/chat, GET /api/Chatbot/available-slots

---

## Tech Stack

- Backend: .NET 8 (ASP.NET Core), EF Core, SignalR
- Frontend: React (Create React App / Vite — choose your starter)
- Database: PostgreSQL
- Containerization: Docker, Docker Compose
- Orchestration: Kubernetes (optional)
- AI: Google Gemini API (Chatbot service)
- Authentication: JWT

---

## Prerequisites

- .NET 8 SDK
- Node.js (>=16) and npm or yarn
- Docker & Docker Compose (if using containers)
- kubectl (if using Kubernetes)
- PostgreSQL (local or container)
- Google Gemini API key (for chatbot)

---

## Repository Layout (example)

- /Authentication_Service
- /Service_Management_Service
- /Notification_Service
- /Chatbot_Service
- /frontend (React app)
- /k8s
- /docker-compose.yml
- /docs (Postman collection, design docs)
- /LICENSE

---

## Quickstart — Local (Development)

1. Clone the repo
   git clone <repository-url>
   cd AutoCare-Backend

2. Start PostgreSQL (local or Docker)
   - Docker:
     docker run --name postgres-autocare -e POSTGRES_PASSWORD=your_password -e POSTGRES_DB=AutoService -p 5432:5432 -d postgres:15

3. Configure environment variables
   - Create .env files for each service (examples below)

4. Run EF Core migrations (for each backend service)
   cd Authentication_Service
   dotnet ef database update

   cd ../Service_Management_Service
   dotnet ef database update

   cd ../Notification_Service
   dotnet ef database update

   cd ../Chatbot_Service
   dotnet ef database update

5. Start backend services (individually)
   # Terminal 1:
   cd Authentication_Service
   dotnet run

   # Terminal 2:
   cd Service_Management_Service
   dotnet run

   # Terminal 3:
   cd Notification_Service
   dotnet run

   # Terminal 4:
   cd Chatbot_Service
   dotnet run

6. Start the React frontend
   cd frontend
   npm install
   npm start
   # App available at http://localhost:3000 (or chosen port)

---

## Frontend (React) — Setup & Environment

Place the React app in /frontend. Example commands:

- Install:
  npm install

- Run dev:
  npm start

- Build:
  npm run build

Sample environment variables (.env.local or .env):

REACT_APP_API_AUTH=http://localhost:5001/api
REACT_APP_API_SERVICE=http://localhost:5002/api
REACT_APP_API_CHATBOT=http://localhost:5004/api
REACT_APP_SIGNALR_URL=http://localhost:5003/notificationHub

Notes:
- Ensure CORS is configured in backend services to allow the React origin (http://localhost:3000).
- For SignalR in the React client use a library like @microsoft/signalr:
  - Connect to `${REACT_APP_SIGNALR_URL}?userId=${userId}` and handle incoming notifications.

---

## Environment Configuration (examples)

Authentication Service (.env)
DATABASE_URL=Host=localhost;Port=5432;Database=AutoService;Username=postgres;Password=your_password
JWT_SECRET=your_jwt_secret_key
JWT_ISSUER=AutoCare
JWT_AUDIENCE=AutoCare

Service Management Service (.env)
DATABASE_URL=Host=localhost;Port=5432;Database=AutoService;Username=postgres;Password=your_password
AUTH_SERVICE_URL=http://localhost:5001

Notification Service (.env)
DATABASE_URL=Host=localhost;Port=5432;Database=AutoService;Username=postgres;Password=your_password

Chatbot Service (.env)
DATABASE_URL=Host=localhost;Port=5432;Database=AutoService;Username=postgres;Password=your_password
GEMINI__APIKEY=your_gemini_api_key

React frontend (.env.local)
REACT_APP_API_BASE=http://localhost:5002
REACT_APP_CHATBOT_API=http://localhost:5004
REACT_APP_SIGNALR_URL=http://localhost:5003/notificationHub

---

## Docker Compose

A docker-compose.yml should define:
- postgres service
- authentication service
- service management service
- notification service
- chatbot service
- frontend service (optional)

Typical flow:
- Build images for each service
- Run migrate containers or run migrations at startup
- Expose ports as required

(If you want, I can generate a sample docker-compose.yml tailored to your service layout.)

---

## Kubernetes

The repository should contain /k8s manifests for:
- Deployments & Services (one per microservice)
- ConfigMaps / Secrets for environment configuration
- StatefulSet or managed PostgreSQL or use external DB
- Ingress or LoadBalancer for external access
- HPA (optional) for scaling

(If you want manifests created, I can scaffold them.)

---

## API Documentation

Each service exposes Swagger UI:

- Authentication: http://localhost:5001/swagger
- Service Management: http://localhost:5002/swagger
- Notification: http://localhost:5003/swagger
- Chatbot: http://localhost:5004/swagger

Use Swagger to explore endpoints and request/response models.

---

## SignalR (Realtime) Integration

- Notification Hub path: /notificationHub
- Client connection: ws://localhost:5003/notificationHub?userId={userId}
- On the server: the Notification Service triggers notifications on appointment changes and sends messages to connected clients.
- In React use @microsoft/signalr to create a HubConnection and subscribe to events like "ReceiveNotification".

Example (React pseudocode):
const connection = new HubConnectionBuilder()
  .withUrl(`${process.env.REACT_APP_SIGNALR_URL}?userId=${userId}`)
  .build();

connection.on("ReceiveNotification", msg => {
  // handle notification
});

---

## Chatbot (Google Gemini)

- Chatbot Service uses Gemini API key to call the model and generate responses.
- Ensure GEMINI__APIKEY is set in the Chatbot Service environment.
- The Chatbot can also query the Service Management DB for availability and context (read-only).
- Endpoint: POST /api/Chatbot/chat — send user message in body; returns AI reply and optional suggested actions (book appointment, check slots).

Security note: Protect API keys in production with Secrets manager (K8s Secrets, Vault, etc).

---

## Testing

- Unit tests: each service should have a test project (xUnit / NUnit).
- Integration tests: spin up a test PostgreSQL instance (Docker) and run API tests against in-memory or test fixtures.
- Manual: Use the Postman collection in /docs to exercise APIs and the chatbot.
- SignalR testing: use a simple SignalR client or the React app to validate real-time delivery.

---

## CI / CD Recommendations

- Use GitHub Actions or GitLab CI to:
  - Run unit & integration tests
  - Build Docker images and push to registry
  - Deploy to staging & production clusters
- Use health checks and readiness probes for Kubernetes deployments
- Use database migration jobs (or tools like Flyway) during deployment

---

## Security & Best Practices

- Keep JWT secret and Gemini API keys out of source control
- Use HTTPS in production and secure SignalR with TLS (wss)
- Rate limit chatbot endpoint if public
- Use role-based permissions for sensitive operations
- Run DB backups and migrations carefully (shared DB pattern increases coupling; consider a messaging/event bus if you need stronger decoupling)

---

## Contributing

- Fork the repository
- Create a branch: git checkout -b feature/your-feature
- Commit changes: git commit -m "Add feature"
- Push and open a Pull Request
- Follow coding standards in each service (formatting, linting)
- Add tests for new features

---

## Support & Contact

- For support, create an issue in this repository or email support@autoservice.com
- Include logs, steps to reproduce, and relevant service names/ports for faster help

---

## License

This project is licensed under the MIT License — see the LICENSE file for details.

---

## Changelog

- v1.0.0 — Initial release
- v1.1.0 — AI chatbot and real-time notifications added
- v1.2.0 — Kubernetes deployment support
