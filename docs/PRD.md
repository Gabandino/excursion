# 🧠 AI JobMatch & Resume Optimizer – Product Requirements Document (PRD)

## 1. Overview
**AI JobMatch** is a full-stack AI-powered web app that:
- Scrapes and analyzes LinkedIn job postings based on a user’s resume and interests.  
- Calculates a **Job Match % Score** for each posting.  
- Reviews and optimizes the user’s resume (style, structure, and content).  
- Auto-generates a **customized cover letter** per job.

The goal is to help users find relevant jobs *faster*, improve resume quality, and automate cover letter creation.

---

## 2. Objectives

| Goal | Description |
|------|--------------|
| 🔍 Intelligent job search | Extracts user skills and experience to find and summarize relevant LinkedIn postings. |
| 🧾 Resume optimization | AI evaluates style, keyword relevance, and flow; gives improvement pointers. |
| 💌 Cover letter generation | AI crafts personalized cover letters using user + job data. |
| 📊 Job match scoring | Provides an explainable match % for each job. |

---

## 3. Target Users
- Job seekers and professionals switching industries  
- Students entering the workforce  
- Anyone seeking AI assistance to enhance job applications  

---

## 4. Core Features

### 4.1 Resume Parser
**Input:** PDF, DOCX, or plain text resume  
**Output:** Extracted `skills`, `roles`, `industries`, `experience_years`, and `achievements`

**Implementation**
- Use `pdfminer.six` or `python-docx` for parsing.  
- Use `spaCy` for Named Entity Recognition (NER).  
- Use OpenAI embeddings to vectorize extracted skills and roles.

---

### 4.2 Job Research & Matching Engine
**Input:** Parsed resume + user preferences (desired roles, skills, locations)  
**Process:**
1. Query LinkedIn Jobs API (or fallback to SerpAPI scraping).
2. Summarize relevant postings.
3. Compute **Match %** based on semantic similarity between resume embeddings and job postings.

**Match Formula**
```python
match_score = (
    skill_overlap * 0.4 +
    exp_relevance * 0.3 +
    title_similarity * 0.15 +
    location_match * 0.1 +
    tone_alignment * 0.05
)
```

**Implementation**

* Use **LangChain + OpenAI embeddings** for semantic comparison.
* Store data in **PostgreSQL** and job embeddings in **Pinecone or FAISS**.

---

### 4.3 Resume Optimizer

Analyzes clarity, structure, ATS-friendliness, and keyword strength.

**Prompt Example**

```text
Analyze this resume and return JSON:
{
  "clarity": int,
  "structure": int,
  "keyword_relevance": int,
  "ATS_ready": int,
  "suggestions": [list of strings]
}
```

**UI Output**

* Bar charts or progress rings for each category.
* Suggestion list displayed under analysis summary.

---

### 4.4 Cover Letter Generator

**Input:** User profile + job posting
**Output:** Personalized 200–250-word cover letter

**Prompt Example**

```text
Write a professional cover letter using this resume and job description.
Tone: {tone}. Limit to 250 words.
```

**Tone Options:** Formal / Conversational / Concise
**Frontend:** Rich text editor with live GPT output.

---

### 4.5 Dashboard & Job Tracker

| Job Title | Company   | Match % | Status  | Date Saved   |
| --------- | --------- | ------- | ------- | ------------ |
| Analyst   | Accenture | 87%     | Applied | Oct 24, 2025 |

**Implementation**

* Frontend: React DataTable + Tailwind
* Backend: CRUD API endpoints for jobs and user tracking

---

## 5. Software Stack (for Codex)

| Layer               | Technology                                     | Purpose                                       |
| ------------------- | ---------------------------------------------- | --------------------------------------------- |
| **Frontend**        | Next.js + TypeScript + Tailwind CSS            | Reactive UI, resume upload, dashboards        |
| **Backend API**     | FastAPI (Python)                               | Resume parsing, job matching, GPT integration |
| **Database**        | PostgreSQL + Prisma ORM                        | Store user, job, and application data         |
| **Vector Store**    | Pinecone / FAISS                               | Store and query embeddings                    |
| **AI Layer**        | OpenAI GPT-4 or GPT-5                          | Resume analysis, match scoring, cover letters |
| **Scraping / Feed** | LinkedIn Jobs API / SerpAPI / Puppeteer        | Fetch job postings                            |
| **Auth**            | NextAuth.js (LinkedIn & Google OAuth)          | Secure user login                             |
| **File Storage**    | AWS S3 / Firebase Storage                      | Resume file storage                           |
| **Hosting**         | Vercel (Frontend) + Railway / Render (Backend) | Scalable deployment                           |

---

## 6. API Endpoints

| Method | Endpoint                     | Description                              |
| ------ | ---------------------------- | ---------------------------------------- |
| `POST` | `/api/upload-resume`         | Uploads and parses resume, returns JSON  |
| `GET`  | `/api/jobs`                  | Fetches job listings from LinkedIn       |
| `POST` | `/api/match`                 | Calculates Match %                       |
| `POST` | `/api/optimize-resume`       | Returns resume improvement suggestions   |
| `POST` | `/api/generate-cover-letter` | Generates cover letter from resume + job |
| `GET`  | `/api/dashboard`             | Returns user’s saved jobs and analytics  |

---

## 7. AI Prompts

### A. Resume Extraction

> Extract job titles, skills, years of experience, education, and quantifiable achievements from this resume. Return JSON.

### B. Job Matching

> Compare this resume summary with the job description. Return a relevance score (0–100) and a one-sentence reasoning.

### C. Resume Feedback

> Critique the resume’s clarity, keyword density, and professional tone. Provide 5 bullet-point improvements.

### D. Cover Letter

> Write a customized, 250-word professional cover letter using the user’s resume and job description.

---

## 8. Monetization Plan

| Tier                    | Description                                        |
| ----------------------- | -------------------------------------------------- |
| **Free**                | 3 job searches/day + 1 cover letter                |
| **Pro ($15/mo)**        | Unlimited searches, resume analysis, cover letters |
| **Enterprise ($50/mo)** | Custom dashboards, analytics, PDF export           |

---

## 9. Phase 2 – Future Enhancements

* Chrome Extension: scan LinkedIn job → instant Match %
* AI Interview Prep: generate mock questions from job post
* Resume → LinkedIn Profile rewrite automation
* Salary intelligence from public databases

---

## 10. Success Metrics

| Metric                          | Target |
| ------------------------------- | ------ |
| Match score accuracy            | ≥ 85%  |
| Resume feedback satisfaction    | ≥ 90%  |
| Cover letter generation latency | < 10s  |
| Paid conversion rate            | ≥ 8%   |

---

## 11. Folder Structure

```
/jobmatch-ai
│
├── /frontend
│   ├── pages/
│   ├── components/
│   ├── utils/
│   └── styles/
│
├── /backend
│   ├── main.py
│   ├── routes/
│   ├── services/
│   ├── models/
│   └── embeddings/
│
├── /database
│   ├── schema.prisma
│   └── migrations/
│
└── /docs
    └── PRD.md
```

---

## 12. Notes for Codex Agent

* Prioritize **modular, API-first** architecture.
* Use `.env` for OpenAI API keys and database credentials.
* Use `langchain` wrappers for GPT prompts to maintain structure.
* Use **TypeScript interfaces** for Resume, Job, and Match data models.
* Include unit tests for all endpoints.

---

**End of PRD**

```

---
```
