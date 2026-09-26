
# Multilingual Legal Simplifier

> **Multilingual Legal Simplifier** is a web-based, AI-driven legal assistant that allows everyday users to upload complex legal documents in various formats (PDF, DOCX, or scanned Images) and receive an instant, plain-language summary, risk analysis with mitigations, and key terms in their chosen target language (supporting 20+ languages). It also offers an interactive RAG-based chat to ask questions directly about their documents.

---

## 🚀 Key Features

*   **Multilingual Processing & Translation:** Auto-detects the source document's language and translates the summaries, explanations, mitigations, and key terms into a user-specified output language.
*   **Plain-Language Summaries:** Translates dense legal wording ("legalese") into a clear 3-5 sentence plain-English (or target language) overview.
*   **Risk Clause Detection:** Identifies risky clauses, classifies their severity (High / Medium / Low), explains the risk simply, and provides actionable counter-proposals or solutions.
*   **Key Terms Extraction:** Highlights crucial elements such as Notice Periods, Financial Amounts, Due Dates, and Penalties.
*   **Multi-Format File Support:** Supports **PDFs** (using `pdfplumber`), **Word documents** (using `python-docx`), and **Scanned Images** (using Vision-based OCR).
*   **Interactive Legal Chat (RAG):** Context-aware, streaming chat assistant that answers user queries about the document using **Rank-BM25 Lexical NLP RAG**.
*   **Secure Symmetric Encryption:** Cryptographically encrypts original file binaries and extracted texts using **AES-256 equivalent Fernet encryption** before database storage, ensuring strict privacy.
*   **User History & Management:** Secure registration/login system with JWT tokens, cached profiles, password resets, and history management.

---

## 🛠️ Tech Stack

### Frontend
*   **Framework:** React.js (Vite)
*   **Styling:** Tailwind CSS, PostCSS
*   **Routing:** React Router DOM (v6)
*   **HTTP Client:** Axios
*   **Internationalization:** i18next & react-i18next
*   **Notifications:** React Hot Toast

### Backend
*   **Framework:** FastAPI (Python 3.10+)
*   **Server:** Uvicorn
*   **Database:** SQLite (managed asynchronously via `aiosqlite`)
*   **Encryption:** Cryptography (Fernet symmetric encryption)
*   **Authentication:** JWT (JSON Web Tokens via `python-jose` + `bcrypt` hashing)
*   **Parsing/OCR:** `pdfplumber` (PDF), `python-docx` (Word), Groq Vision OCR (Images)
*   **AI Engine:** Groq API (`llama-3.3-70b-versatile` for analysis and chat, `meta-llama/llama-4-scout-17b-16e-instruct` for OCR)
*   **Search/RAG:** Rank-BM25 (lexical ranking for query context matching)

---

## 📁 Project Structure

```
multilingual_legal_simplifier/
│
├── legalease-backend/           # FastAPI Backend
│   ├── app/
│   │   ├── models/              # Pydantic schemas (user.py, document.py)
│   │   ├── routes/              # Route controllers (auth.py, document.py)
│   │   ├── services/            # Core business logic (ai_summarizer, file_extractor, etc.)
│   │   ├── utils/               # JWT helper functions (auth_utils.py)
│   │   ├── config.py            # Environment configurations loading
│   │   ├── database.py          # SQLite database connection & schema tables
│   │   └── main.py              # Application startup & router aggregation
│   ├── .env                     # Local environment settings
│   ├── legalease.db             # Local SQLite database file
│   └── requirements.txt         # Python server dependencies
│
└── legalease-frontend/          # React Frontend (Vite)
    ├── src/
    │   ├── api/                 # Axios configuration and backend request helpers
    │   ├── components/          # Reusable UI widgets (UploadBox, ClauseCard, Navbar, etc.)
    │   ├── context/             # React Context for global auth state
    │   ├── locales/             # i18n localization translation JSON files
    │   ├── pages/               # Page components (Landing, Login, Register, Upload, Result, History)
    │   ├── utils/               # Helper utilities and formatting functions
    │   ├── App.jsx              # Application router wrapper
    │   ├── constants.js         # Frontend configuration constants
    │   ├── i18n.js              # Localization config script
    │   ├── index.css            # Tailwind styles import
    │   └── main.jsx             # React DOM root render
    ├── index.html               # Main HTML shell
    ├── package.json             # Frontend package configurations
    ├── tailwind.config.js       # Custom Tailwind parameters
    └── vite.config.js           # Vite server settings
```

---

## ⚙️ Local Setup & Configuration

### Prerequisites
*   Node.js (v18+)
*   Python (v3.10+)
*   Groq API Key (Sign up and get an API key at [console.groq.com](https://console.groq.com))

---

### 1. Clone the Repository
```bash
git clone https://github.com/your-username/multilingual_legal_simplifier.git
cd multilingual_legal_simplifier
```

---

### 2. Backend Setup

1.  Navigate to the backend directory:
    ```bash
    cd legalease-backend
    ```

2.  Create and activate a virtual environment:
    ```bash
    # Windows
    python -m venv venv
    venv\Scripts\activate

    # macOS/Linux
    python3 -m venv venv
    source venv/bin/activate
    ```

3.  Install the required dependencies:
    ```bash
    pip install -r requirements.txt
    ```

4.  Configure the environment variables by creating a `.env` file in the `legalease-backend` directory:
    ```env
    # Security Key for hashing and JWT signing
    SECRET_KEY=your_super_secret_jwt_key_at_least_32_characters_long

    # Symmetric Key for encrypting stored document contents (Generate one using cryptography.fernet.Fernet.generate_key().decode())
    FILE_ENCRYPTION_KEY=your_generated_fernet_encryption_key_here

    # Groq Cloud API credentials
    GROQ_API_KEY=gsk_your_groq_api_key_goes_here

    # SMTP Configuration for Resetting Passwords
    SMTP_EMAIL=your_email@gmail.com
    SMTP_PASSWORD=your_16_digit_app_specific_password_here
    ```

5.  Run the FastAPI development server:
    ```bash
    uvicorn app.main:app --reload --port 8000
    ```
    *   **API Root URL:** `http://localhost:8000`
    *   **Swagger Documentation:** `http://localhost:8000/docs`

---

### 3. Frontend Setup

1.  Navigate to the frontend directory:
    ```bash
    cd ../legalease-frontend
    ```

2.  Install packages:
    ```bash
    npm install
    ```

3.  Configure local environment endpoint. Create a `.env` file in `legalease-frontend/`:
    ```env
    VITE_API_URL=http://localhost:8000
    ```

4.  Start Vite development server:
    ```bash
    npm run dev
    ```
    *   **Local Web Server URL:** `http://localhost:5173`

---

## 📡 API Reference

### Authentication Routes (`/api/auth`)
| Method | Endpoint | Description | Authentication |
| :--- | :--- | :--- | :--- |
| `POST` | `/register` | Sign up a new user account | None |
| `POST` | `/login` | Authenticate credentials & retrieve JWT | None |
| `GET` | `/me` | Get profile information for logged-in user | JWT Bearer Token |
| `DELETE`| `/me` | Permanently deletes user account and their documents | JWT Bearer Token |
| `POST` | `/forgot-password` | Generate reset token and email it to the user | None |
| `POST` | `/reset-password` | Validate reset token and overwrite user password | None |

### Document Management Routes (`/api/document`)
| Method | Endpoint | Description | Authentication |
| :--- | :--- | :--- | :--- |
| `GET` | `/languages` | Fetch the list of supported output translation languages | None |
| `POST` | `/upload` | Upload PDF/DOCX/Image, extract text, run analysis, store encrypted | JWT Bearer Token |
| `GET` | `/history` | Fetch history of uploaded documents (truncated summaries) | JWT Bearer Token |
| `GET` | `/{doc_id}` | Fetch full details (summary, risks, key terms) of a document | JWT Bearer Token |
| `DELETE`| `/{doc_id}` | Delete a specific document from history | JWT Bearer Token |
| `GET` | `/{doc_id}/download` | Decrypt and download the original uploaded document | JWT Bearer Token |
| `POST` | `/{doc_id}/chat` | Query the document and stream an interactive AI reply (BM25 RAG) | JWT Bearer Token |

---

## 🔐 Security Standards

1.  **Symmetric AES-256 (Fernet) Encryption:** Rather than storing plain text files or raw binaries in the SQLite database, all document bytes and extracted text blocks are encrypted before DB write, and decrypted on retrieve.
2.  **Cryptographic Password Hashing:** User passwords are encrypted on register and verified on login using `bcrypt` salting.
3.  **JSON Web Tokens (JWT):** API calls to all sensitive `/me` and `/document` resources require a valid JSON Web Token sent inside the `Authorization: Bearer <token>` header.
4.  **Ownership Isolation:** Users can only view, download, query, or delete documents which they uploaded (validated via the `userId` column match in SQLite queries).

---

## ⚠️ Disclaimer

Multilingual legal simplifier is for informational and educational purposes only. The summary, analysis, and recommendations provided by the AI do not constitute official legal advice. Users should always consult with a certified attorney or legal professional before signing or executing any legal agreements.
