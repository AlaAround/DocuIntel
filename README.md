# DocuIntel — Intelligent PDF Document Analysis & Summarization

[![.NET 8](https://img.shields.io/badge/.NET-8.0-512BD4?logo=dotnet)](https://dotnet.microsoft.com/)
[![React 18](https://img.shields.io/badge/React-18.3-61DAFB?logo=react)](https://react.dev/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.0-3178C6?logo=typescript)](https://www.typescriptlang.org/)
[![Vite](https://img.shields.io/badge/Vite-6.3-646CFF?logo=vite)](https://vitejs.dev/)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-v4-38B2AC?logo=tailwind-css)](https://tailwindcss.com/)
[![Gemini AI](https://img.shields.io/badge/Google_Gemini-3.5_Flash-4285F4?logo=google)](https://ai.google.dev/)
[![Python](https://img.shields.io/badge/Python-3.10+-3776AB?logo=python)](https://python.org/)

An enterprise-grade, full-stack document intelligence application. Upload any PDF document to automatically classify its taxonomy (e.g., Insurance Policy, Healthcare Record, Business Invoice) and generate executive summaries and structured key findings powered by **Google Gemini 3.5**.

---

## Screenshots

### 1. Document Analysis Workspace
> Complete executive summary, taxonomy badge, structured takeaways, and instant export options (Markdown, JSON, Clipboard).

![Document Analysis Workspace](screenshots/screenshot-3.png)

---

### 2. Document Ingestion & Upload
> Drag-and-drop PDF dropzone with real-time size validation and multi-step pipeline status.

![Document Ingestion](screenshots/screenshot-2.png)

---

### 3. Pipeline Ingestion & Heuristics
> Clean processing states with automatic detection and graceful handling for empty or scanned non-text documents.

![Processing State](screenshots/screenshot-1.png)

---

## Architecture Overview

```
┌────────────────────────────────────────────────────────┐
│               React + Vite Frontend (UI)               │
│   • Drag-and-drop PDF Ingestion                        │
│   • Real-time Multi-step Processing Stepper            │
│   • Taxonomy Badging & Executive Summary Display       │
│   • One-click Export to Markdown & JSON                │
└───────────────────────────┬────────────────────────────┘
                            │ HTTP POST (multipart/form-data)
                            ▼
┌────────────────────────────────────────────────────────┐
│             ASP.NET Core 8 Web API (Backend)           │
│   • File stream validation (type, size <= 10MB)        │
│   • Secure temporary file management (GUID-based)      │
│   • Process orchestration (spawns Python worker)       │
│   • Serves static SPA dist assets in production        │
└───────────────────────────┬────────────────────────────┘
                            │ CLI Invocation (process_pdf.py)
                            ▼
┌────────────────────────────────────────────────────────┐
│            Python Document Extraction Worker           │
│   • PDF Stream Text Extraction (pypdf)                 │
│   • Domain Taxonomy Classifier (Insurance/Medical/Gen) │
│   • Tier 1: Google Gemini 3.5 Flash / Flash-Lite API   │
│   • Tier 2: OpenAI GPT-4.1-mini Fallback               │
│   • Tier 3: Local Rule-Based Lexical Extractor         │
└────────────────────────────────────────────────────────┘
```

---

## Key Features

- **Multi-Model LLM Resilience**:
  - Primary: **Google Gemini 3.5 Flash** (via the official `google-genai` SDK) for fast, structured document analysis.
  - Secondary: **OpenAI GPT-4.1-mini** fallback if Gemini is unreachable.
  - Offline / Rule-Based: Local extraction heuristics if no API keys are present.
- **Automated Taxonomy Classification**:
  - Identifies domain types including **Health Insurance Policies**, **Clinical Healthcare Records**, and **General Invoices/Documents**.
- **Modern Enterprise Interface**:
  - Clean, distraction-free neutral palette inspired by modern developer platforms (Linear, Stripe, Vercel).
  - Responsive layout built with Tailwind CSS v4, Lucide icons, and Radix UI primitives.
- **Export & Productivity Tools**:
  - One-click **Markdown** report generation and download.
  - One-click **Raw JSON** schema export.
  - Copy individual takeaway insights or the entire briefing to the clipboard.
- **Resilient File Handling**:
  - Strict validation for PDF file signature and size bounds.
  - Graceful notification when documents lack readable text layers (scanned images without OCR).
  - Cross-platform Windows file path compatibility patch for paths containing special characters (`#`).

---

## Tech Stack

| Layer | Technology | Details |
|---|---|---|
| **Frontend** | React 18, TypeScript, Vite 6 | Fast modern SPA with HMR and strict typing |
| **Styling** | Tailwind CSS v4, Lucide React | Clean, responsive, enterprise design system |
| **Backend API** | ASP.NET Core 8 (.NET 8) | High-performance C# Minimal API with CORS |
| **AI Inference** | Python 3.10+ & `google-genai` | Native Google GenAI SDK with structured JSON schemas |
| **PDF Extraction** | `pypdf`, `reportlab` | Stream parsing and testing harness |

---

## Project Structure

```
├── back/                         # ASP.NET Core 8 Backend & Python Worker
│   ├── .env                      # Environment file containing GEMINI_API_KEY
│   ├── PdfUploadApi.csproj       # .NET 8 project configuration
│   ├── Program.cs                # Minimal API endpoints, CORS, static file hosting
│   ├── requirements.txt          # Python dependencies (google-genai, pypdf, etc.)
│   └── Scripts/
│       └── process_pdf.py        # PDF text parser, classifier & Gemini LLM engine
├── front/                        # React + Vite Frontend
│   ├── index.html                # HTML entry point
│   ├── package.json              # NPM dependencies & scripts
│   ├── patch_vite.cjs            # Path normalization patch for paths with '#'
│   ├── vite.config.ts            # Vite build configuration
│   └── src/
│       ├── main.tsx              # React DOM mounting
│       ├── app/
│       │   ├── App.tsx           # DocuIntel Studio main application component
│       │   └── components/       # Radix UI and enterprise component library
│       └── styles/               # Tailwind CSS and theme tokens
├── screenshots/                  # Project screenshots for documentation
│   ├── screenshot-1.png          # Processing & extraction state
│   ├── screenshot-2.png          # Ingestion dropzone & file selection
│   └── screenshot-3.png          # Executive analysis & structured output
└── README.md                     # Comprehensive project documentation
```

---

## Getting Started

### 1. Prerequisites
- [.NET 8.0 SDK](https://dotnet.microsoft.com/download/dotnet/8.0)
- [Node.js 18+ & npm](https://nodejs.org/)
- [Python 3.10+](https://www.python.org/)

---

### 2. Configure Your API Key

#### Option A: Project `.env` file (Recommended)
Place your Gemini API key in `back/.env`:
```env
GEMINI_API_KEY=your_gemini_api_key_here
```

#### Option B: Environment Variable (PowerShell)
```powershell
$env:GEMINI_API_KEY = "your_gemini_api_key_here"
```

---

### 3. Install Backend & Python Dependencies

Open a terminal in the `back/` directory:
```powershell
cd back
python -m pip install -r requirements.txt
```

*Installed libraries:*
- `pypdf` (Text extraction)
- `google-genai` (Google Gemini SDK)
- `openai` (Optional fallback)
- `reportlab` (Test PDF generation)

---

### 4. Install Frontend Dependencies

Open a second terminal in the `front/` directory:
```powershell
cd front
npm install
```

> **Note on Windows paths with `#` (e.g. `c#net`):**  
> `npm install` automatically runs `patch_vite.cjs` via a post-install hook to guarantee Vite and Tailwind cleanly resolve paths without hash-fragment stripping.

---

### 5. Running the Application

#### Development Mode (Recommended)

1. **Start the ASP.NET Core Backend** (Port 5000):
   ```powershell
   cd back
   dotnet run --urls "http://localhost:5000"
   ```

2. **Start the Vite Frontend** (Port 5173):
   ```powershell
   cd front
   npm run dev
   ```

3. Open your browser to **[http://localhost:5173/](http://localhost:5173/)**.

---

#### Production Single-Port Mode

You can also compile the frontend and serve everything directly from the .NET backend:

1. **Build Frontend**:
   ```powershell
   cd front
   npm run build
   ```
2. **Run Backend**:
   ```powershell
   cd back
   dotnet run --urls "http://localhost:5000"
   ```
3. Open **[http://localhost:5000/](http://localhost:5000/)** directly.

---

## API Reference

### `POST /api/summarize-pdf`

Uploads and processes a PDF file.

- **Content-Type**: `multipart/form-data`
- **Payload**:
  - `file`: Binary PDF file (Max 10 MB).

#### Success Response (`200 OK`)
```json
{
  "documentType": "Health Insurance Policy Renewal",
  "summary": "This document outlines the health insurance policy coverage renewal details for Jane Smith, including covered medical services, premium information, and claim submission timelines.",
  "keyPoints": [
    "Policy POL-982341 is issued to Jane Smith for health insurance renewal.",
    "Coverage includes comprehensive outpatient care, prescription drug co-pays, and emergency hospital stays.",
    "Medical claims must be filed within 90 days of the service date."
  ],
  "source": "Gemini"
}
```

#### Error Response (`400 Bad Request`)
```json
{
  "error": "Only PDF files are supported."
}
```

---

## License

This project is licensed under the [MIT License](LICENSE).
