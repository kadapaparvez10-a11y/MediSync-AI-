# CareJourney - Healthcare Coordination Platform & AI Clinical OS

A full-stack healthcare coordination system connecting patients, phlebotomists/diagnostic laboratories, specialist physicians, pharmacies, and longitudinal health monitoring.

## 🚀 Key Features & Capabilities

### 1. Patient Portal
- **AI Health Intake Agent (Agent 1 & 2)**: Patients report acute symptoms and chronic histories; the AI triage assistant categorizes severity, recommends evidence-based laboratory panels, and schedules sample collection.
- **AI Email Agent Inbox**: A dedicated patient email client receiving encrypted clinical notifications whenever diagnostic reports are certified. Supports rich HTML emails, biomarker attention flags, plain text, and cryptographic TLS receipts.
- **My Reports & Biomarker Analysis**: Interactive parameter breakdown (Normal, High, Critical) with longitudinal graphs and AI clinical summaries.
- **Specialist Matchmaker & Teleconsultations**: Algorithmic specialist doctor discovery with in-app WebRTC video consultation room.
- **Digital Prescriptions & Pharmacy Tracking**: Direct courier tracking across 4 delivery stages (*Out for Delivery*, *On the Way*, *Reached*, *Received*).
- **Proactive Health Trends**: Longitudinal HbA1c, fasting glucose, and blood pressure monitoring across clinical cycles.

### 2. Lab Assistant Portal
- **Sample Collection Requests**: Phlebotomy logistics with barcode assignment, sterile cold-chain temperature monitoring (2°C - 8°C), and patient address guidance.
- **Report Certification Form**: Pathological parameters entry with reference ranges.
- **AI Email Agent Dispatch**: Automatic, HIPAA-compliant patient email notification with live email draft preview and instant delivery receipt.

### 3. Specialist Doctor Portal
- **Consultation Triage**: Pending appointment review with patient history, recent diagnostic parameters, and vitals.
- **Prescription Builder**: Cryptographically signed digital prescriptions with dosage, frequency, duration, and automatic routing to pharmacies.
- **Continuous Monitoring Plans**: Enrollment into recurring diagnostic cycles.

### 4. Medical Shop & Pharmacy Portal
- **Prescription Orders Queue**: Verified digital prescriptions with batch verification.
- **Dispensary Workflow**: Multi-stage delivery coordination (*Out for Delivery*, *On the Way*, *Reached*, *Received*).

---

## 🛠️ Technology Stack
- **Frontend**: React 19, TypeScript, Tailwind CSS, Lucide Icons, Vite
- **Backend**: Node.js, Express, TypeScript (`tsx server.ts`)
- **AI Engine**: Google GenAI SDK (`@google/genai`) with clinical fallback circuit-breakers
- **Data Store**: In-memory relational store with audit logs and HIPAA-aligned security protocols

---

## 💻 Getting Started Locally

### 1. Prerequisites
- Node.js 18+ or Bun
- npm or bun

### 2. Installation & Running
```bash
# Clone or unzip repository
unzip healthcare-application-source.zip -d carejourney
cd carejourney

# Install dependencies
npm install

# (Optional) Configure environment variables
cp .env.example .env

# Start development server (Full-Stack: Express API + Vite Frontend on port 3000)
npm run dev
```

Visit `http://localhost:3000` in Google Chrome, Microsoft Edge, or Mozilla Firefox.

---

## 🔑 Environment Variables & API Keys

| Variable | Required | Description | Default / Fallback |
| :--- | :--- | :--- | :--- |
| `GEMINI_API_KEY` | Optional | Google Gemini API Key for dynamic real-time AI triage and analysis | Built-in clinical fallback engine engages if key is omitted |
| `PORT` | Optional | Port for the Node.js Express server | Defaults to `3000` |
| `APP_URL` | Optional | Fully qualified host URL for links in dispatched emails | `http://localhost:3000` |

---

## 🚀 Production Build & Deployment Guide

### Final Production Build Command
```bash
npm run build
```
This triggers:
1. `vite build`: Compiles client-side React 19 SPA assets into `dist/` with Tailwind CSS optimization and code-splitting.
2. `esbuild server.ts`: Bundles the full Express backend into `dist/server.cjs` targeting Node.js runtime.

### Recommended Deployment Options

#### Option A: Docker / Container (Cloud Run, AWS ECS, Fly.io, Railway, Render)
```dockerfile
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM node:20-alpine
WORKDIR /app
COPY --from=builder /app/package*.json ./
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/public ./public
ENV PORT=3000
ENV NODE_ENV=production
EXPOSE 3000
CMD ["npm", "run", "start"]
```

#### Option B: Direct VPS / Server (Ubuntu, Debian, EC2)
```bash
# 1. Install Node.js
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install -y nodejs

# 2. Deploy application
git clone <repo-url> /var/www/carejourney
cd /var/www/carejourney
npm install
npm run build

# 3. Process management with PM2
sudo npm install -g pm2
pm2 start dist/server.cjs --name "carejourney"
pm2 startup
pm2 save
```

#### Option C: Render / Railway / Heroku
- **Build Command**: `npm install && npm run build`
- **Start Command**: `npm run start`
- **Port**: Set by hosting environment via `PORT` variable (handled dynamically by `server.ts`).

---

## 📦 Project Structure
```
├── server/
│   ├── db.ts               # In-memory database store & audit logger
│   ├── emailAgent.ts       # AI Email Agent dispatch & template compiler
│   ├── gemini.ts           # Gemini AI clinical triage & trend analysis
│   └── routes.ts           # REST API endpoints
├── src/
│   ├── components/         # Shared UI components (Navbar, EmailModal, ConsultationRoom, etc.)
│   ├── data/               # Initial clinical demo records
│   ├── pages/
│   │   ├── doctor/         # Specialist Doctor Dashboard
│   │   ├── lab/            # Lab Assistant Phlebotomy & Upload Dashboard
│   │   ├── patient/        # Patient Dashboard, Reports, Email Inbox, Appointments
│   │   └── pharmacy/       # Medical Shop & Pharmacy Dispensary
│   ├── services/           # Frontend API client
│   └── types/              # Comprehensive TypeScript interfaces
├── server.ts               # Backend entry point
├── package.json
└── vite.config.ts
```
