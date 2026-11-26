# MedGuard

**Cellular-Mechanism Medicine & Prescription Safety Verifier**

MedGuard is a full-stack clinical safety and pharmacology application that parses prescriptions, verifies daily dosages and disease-indication matches, screens against regulator alerts (CDSCO, WHO, FDA), incorporates patient health & fatal-risk intake, and visualizes drug-target interactions in 3D using Three.js and live RCSB PDB / AlphaFold structures (Mol*) with multilingual support in **English**, **Tamil**, and **Hindi**.

---

## Key Features

- **Health & Safety Quiz (`PatientHealthQuizPage`)**: Captures recent surgeries, drug allergies, and fatal organ risk factors once and combines them into every prescription verification verdict.
- **Prescription Scanner & Audit (`PrescriptionWorkspace`)**: Supports uploading prescription images, tablet strip photos, PDFs, and DOCX files using local extraction (`tesseract.js`, `unpdf`, `mammoth`) with optional Gemini AI enhancement (`@google/genai`). Evaluates disease indication match, daily dosage limits, pediatric RAG guidelines, CDSCO banned fixed-dose combinations (FDCs), and interaction hazards.
- **Clinical Scenario Suite (`TestCasesPage`)**: Built-in curated presets covering safe regimens, `CYP3A4` clashes, overdose thresholds, CDSCO banned FDCs, and pediatric weight warnings.
- **3D Cell Structure Atlas (`CellStructure3D`)**: Interactive 3D eukaryotic cell cross-section built with Three.js showing where medicines act across organelles (ER, mitochondria, Golgi, membrane).
- **AlphaFold & PDB 3D Mechanism Lens (`MechanismLens3D`, `MolStarLiveViewer`)**: Live Mol* molecular viewer fetching structure files and metadata from RCSB PDB and AlphaFold DB for targets `CYP3A4`, `CYP2D6`, `HERG`, `PGP`, `AMPK`, and `COX2`.
- **Remediation Planner (`RemediationPlanPage`)**: Produces prioritized action checklists and safety measures tailored to the verification outcome.
- **Facility Watchlist & Batch Recall Traceback (`FacilityWatchlist`)**: Tracks pharmacy stock batches, inventory ledger movements, stock reconciliation, outbox notifications, and reverse-match sweeps against regulatory safety alerts.
- **Pharmacist Governance Queue (`PharmacistQueue`)**: Review and triage workflow for structural protein hypotheses and molecular evidence.
- **Multilingual Assistant & Voice Readout (`MedGuardChatbot`)**: Interactive guide chatbot and audio readout (`/api/tts` with browser Web Speech API fallback) in English (`en`), Tamil (`ta`), and Hindi (`hi`).
- **Authentication & Security (`firebase.ts`, `firestore.rules`)**: Google Sign-In via Firebase Authentication (with guest access option), persisted language preferences, and saved verification checks protected by strict Firestore security rules (`security_spec.md`, `firestore.rules.test.ts`).

---

## Tech Stack

- **Frontend**: React 19, TypeScript, Vite 8, Tailwind CSS v4, Lucide React, Motion, Three.js, Mol* (`molstar`)
- **Backend**: Node.js, Express (`server.ts` via `tsx`), `@google/genai`, `tesseract.js`, `unpdf`, `mammoth`, `dotenv`
- **Auth & Database**: Firebase Authentication & Cloud Firestore

---

## Project Structure

```text
med-main/
├── server.ts                       # Express server, Vite middleware & API endpoints
├── index.html                      # App entry HTML & fonts
├── package.json                    # Scripts and dependencies
├── .env.example                    # Environment variable template
├── firebase-applet-config.json     # Firebase client configuration
├── firebase-blueprint.json         # Firestore entity schemas
├── firestore.rules                 # Cloud Firestore security rules
├── firestore.rules.test.ts         # Security rules test suite
├── security_spec.md                # Firestore security invariants & threat model
├── public/                         # Static assets & OCR/bundled resources
└── src/
    ├── App.tsx                     # Root application shell, routing & TTS readout
    ├── firebase.ts                 # Firebase initialization & Firestore helpers
    ├── components/                 # UI pages, 3D viewers, scanner, watchlist & chatbot
    ├── data/                       # Medication database, pediatric RAG & catalogs
    ├── server/                     # Document OCR/PDF/DOCX extractor, guide chat & planner
    ├── shared/                     # Core deterministic prescription verifier
    ├── types/                      # TypeScript domain types
    └── utils/                      # Dosage evaluation helpers
```

---

## Getting Started

### Prerequisites

- Node.js (v20+ recommended)
- npm (or Bun)
- A Google Gemini API key (optional, for AI-assisted OCR parsing, explanations, TTS, and guide chat; deterministic verification and local OCR work without it)

### Installation

1. Install dependencies:

   ```bash
   npm install
   ```

2. Create a `.env` file from `.env.example`:

   ```bash
   cp .env.example .env
   ```

   Update `.env` with your values:

   ```env
   GEMINI_API_KEY="YOUR_GEMINI_API_KEY"
   APP_URL="http://localhost:3000"
   ```

### Running Locally

Start the development server (Express API + Vite middleware on port `3000` by default):

```bash
npm run dev
```

### Other Scripts

- `npm start` — Run the server (`tsx server.ts`)
- `npm run build` — Build the production frontend bundle (`vite build`)
- `npm run preview` — Preview the production build (`vite preview`)
- `npm run lint` — Run TypeScript type-checking (`tsc --noEmit`)

---

## API Endpoints Overview

- `GET /api/health` — Service health probe
- `GET /api/structure/metadata/:targetId` & `GET /api/structure/file` — Live RCSB PDB & AlphaFold DB metadata/file proxy
- `POST /api/prescription/ocr-parse` — Extract and parse prescription items from images, PDFs, or DOCX files
- `POST /api/prescription/verify` — Run deterministic dosage, indication, interaction, and regulator alert verification
- `POST /api/prescription/explain` — Generate multilingual patient-friendly explanations
- `POST /api/prescription/remediation-plan` — Build prioritized clinical remediation steps
- `GET /api/rag/records` & `POST /api/rag/evaluate-dosage` — Pediatric/clinical RAG records and age/condition dosage evaluation
- `POST /api/tts` & `POST /api/chat/guide` — Multilingual speech synthesis and guide assistant
- `GET/POST /api/facility/*`, `GET /api/clinical/rules`, `GET/POST /api/pharmacist/*` — Facility stock watchlist, ledger reconciliation, outbox notifications, and pharmacist queue APIs
