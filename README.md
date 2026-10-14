# 🎓 SmartStudy Assistant

### Understand concepts. Find your gaps. Review with a plan.

**SmartStudy Assistant (SSA)** is an AI-powered web application that guides students through a complete study cycle: learning a topic, testing their understanding, identifying weak concepts, revisiting those concepts, and scheduling future revision.

Students can start with a **topic or a text-based PDF**. The application combines AI-generated learning content with assessment logic and the **SM-2 spaced repetition algorithm** to organize what they should study next.

[🌐 Website](https://smartstudy-assistant.onrender.com/) · [💻 Source Code](https://github.com/IMRAN-8/smartstudy-assistant) · [🚀 Getting Started](#getting-started)

---

## What Does This Project Do?

Reading an explanation is only one part of learning. Students also need to check what they understood, identify mistakes, and revisit difficult ideas.

SmartStudy Assistant brings these steps into one application. Its goal is to help students move from simply reading content to **practising, receiving feedback, and following a revision schedule**.

For example, a student studying **Photosynthesis** can read an explanation, answer questions, discover which sub-concepts need more attention, and receive a focused follow-up lesson. Those weak concepts are then added to a review schedule.

### সহজ বাংলায়

**SmartStudy Assistant হলো AI-ভিত্তিক পড়াশোনার একটি ওয়েব অ্যাপ।** শিক্ষার্থী কোনো topic লিখলে বা PDF দিলে এটি বিষয়টি বুঝিয়ে দেয়, প্রশ্ন তৈরি করে এবং উত্তর যাচাই করে। এরপর ভুল বা কম নম্বর পাওয়া অংশ শনাক্ত করে সেই অংশ আবার শেখায়। পরে কখন রিভিশন দিতে হবে, সেটিও নির্ধারণ করে।

## The Learning Cycle

| Stage | What happens |
| --- | --- |
| **1. Start** | Enter a topic or upload a PDF containing readable text. |
| **2. Explain** | Receive an AI-generated explanation with examples, common mistakes, and a summary. |
| **3. Examine** | Answer multiple-choice and short-answer questions about the material. |
| **4. Diagnose** | See your score, answer feedback, and concepts that need more practice. |
| **5. Re-teach** | Study focused explanations, examples, and mini question-and-answer checks for weak concepts. |
| **6. Recall** | Review scheduled concepts and rate recall difficulty to update the next review date. |

## Key Features

- **Topic-based learning:** Start a study session by entering a subject or concept.
- **PDF input:** Extract text from uploaded PDFs using `pdf-parse`.
- **Structured explanations:** Learn through an overview, examples, common mistakes, and a summary.
- **Generated assessments:** The assessment prompt requests five MCQs and three short-answer questions.
- **Answer feedback:** MCQs are checked against stored answers; short answers are evaluated through the AI model.
- **Concept-level diagnosis:** Questions carry concept tags so incorrect or low-scoring answers can be connected to specific learning gaps.
- **Targeted re-teaching:** Generate follow-up lessons for the identified weak concepts.
- **Spaced revision:** Use SM-2 to reschedule reviews based on the student's recall rating.
- **Study history:** View previous study sessions and recorded scores.
- **User-ID workspaces:** Associate study sessions and review items with a chosen user ID.

## How the Main Logic Works

### AI Integration

The backend connects to an existing language model through **OpenRouter**, using the OpenAI-compatible JavaScript SDK. It requests structured JSON for explanations, assessments, short-answer grading, and re-teaching lessons.

This project **integrates an existing AI model**; it does not train a new model. Model selection is configurable through environment variables.

### Assessment and Weak-Concept Detection

- **MCQs:** The server compares the submitted answer with the stored correct answer.
- **Short answers:** The AI returns a score, correctness decision, and brief feedback.
- **Overall score:** Individual question scores are averaged and returned as a percentage.
- **Weak concepts:** `diagnoseWeakConcepts()` groups answers needing improvement by their concept tag. An answer is skipped only when it is marked correct and scores at least `0.7`; scores below `0.4` receive high severity, and other flagged answers receive medium severity.

Weak-concept detection is implemented in application code using grading results. AI then supplies the targeted teaching content.

### Revision Scheduling

High-severity weak concepts are initially scheduled for review after **one day**, and medium-severity concepts after **two days**.

During a review, the student selects **Again, Hard, Good, or Easy**. The `sm2()` function updates the repetition count, ease factor, interval, and next due date. Selecting **Again** resets the repetition count and schedules another review after one day.

## Technology Stack

| Layer | Technology | Purpose |
| --- | --- | --- |
| Frontend | HTML, CSS, vanilla JavaScript | Interface, interactions, and displaying results |
| Backend | Node.js and Express | Serve the frontend and handle API requests |
| Database | PostgreSQL, including Supabase-hosted PostgreSQL | Store sessions, learning content, assessments, and reviews |
| Database client | `pg` | Execute PostgreSQL queries |
| AI integration | OpenRouter with the `openai` SDK | Generate learning content and evaluate short answers |
| File upload | `multer` | Receive uploaded PDFs |
| PDF processing | `pdf-parse` | Extract readable text from PDFs |
| Configuration | `dotenv` | Load local environment variables |

The project runs as **one Node.js application**. Express serves both the static frontend and the backend API; no frontend build step is required.

## Project Structure

| File | Responsibility |
| --- | --- |
| `public/index.html` | Page structure and navigation |
| `public/style.css` | Colours, typography, layout, and visual styling |
| `public/script.js` | Frontend state, screen rendering, interactions, and API calls |
| `server.js` | Express routes, AI integration, grading, diagnosis, and scheduling |
| `schema.sql` | Database tables, indexes, and compatibility migrations |
| `package.json` | Dependencies, Node.js requirement, and start commands |
| `README.md` | Project overview and setup instructions |
| `LICENSE` | MIT license terms |

## Getting Started

### Prerequisites

- Node.js **20 or newer**, as specified in `package.json`, and npm.
- A PostgreSQL database; a Supabase PostgreSQL project can be used.
- An OpenRouter API key with access to the selected model.

### 1. Download the Project

```bash
git clone https://github.com/IMRAN-8/smartstudy-assistant.git
cd smartstudy-assistant
npm install
```

### 2. Set Up the Database

Run the contents of [`schema.sql`](schema.sql) in your PostgreSQL database. For Supabase, use the project's SQL Editor.

The schema stores users, study sessions, explanations, assessments, questions, submissions, answers, weak concepts, re-teaching lessons, and recall schedules.

### 3. Configure Environment Variables

Create a `.env` file in the project root:

```dotenv
PORT=5000
DATABASE_URL=your_postgresql_connection_string
OPENROUTER_API_KEY=your_openrouter_api_key
```

Optional settings supported by the code:

| Variable | Purpose |
| --- | --- |
| `LLM_MODEL` | Override the code's default model, `openai/gpt-oss-120b`. |
| `FALLBACK_MODEL` | Override the fallback model, `openai/gpt-oss-20b`. |
| `OPENROUTER_BASE_URL` | Override the default API base URL. |
| `REASONING_EFFORT` | Set to `off` to disable the reasoning-effort parameter. |
| `NODE_ENV` | Set to `production` for the code's production database SSL configuration. |

The model names above are defaults in the source code; availability depends on the AI provider and your account. Keep API keys and database credentials out of Git commits.

### 4. Start the Application

```bash
npm start
```

Open [http://localhost:5000](http://localhost:5000), choose a user ID, and begin with a topic or PDF.

## Main API Routes

| Method | Endpoint | Purpose |
| --- | --- | --- |
| `POST` | `/api/user` | Register or reuse a user ID |
| `GET` | `/api/sessions` | Retrieve study history |
| `POST` | `/api/session/topic` | Create a topic-based session |
| `POST` | `/api/session/pdf` | Create a PDF-based session |
| `POST` | `/api/explanation` | Generate or retrieve an explanation |
| `POST` | `/api/assessment` | Generate an assessment |
| `POST` | `/api/submit` | Grade answers, identify weak concepts, and schedule reviews |
| `POST` | `/api/reteach` | Generate focused re-teaching lessons |
| `GET` | `/api/recall/due` | Retrieve due review items |
| `POST` | `/api/recall/:id/review` | Record a recall rating and update the schedule |
| `GET` | `/api/health` | Check whether the server is responding |

## Current Scope and Limitations

- **Identity is not authentication:** The app uses a chosen user ID without a password. Anyone who knows an ID can use it; this is not a secure account system.
- **Text-based PDFs:** OCR for scanned or image-only PDFs is not implemented. Uploads are limited to 8 MB, and extracted text stored for a session is capped at 50,000 characters.
- **AI-dependent content:** Explanations and short-answer grades can contain errors and depend on the configured provider being available.
- **In-app review scheduling:** The application shows due reviews; email and push reminders are not implemented.
- **Learning outcomes:** The project aims to support learning and retention; measured improvements have not been established by an evaluation in the reviewed code.

## Possible Future Improvements

- Secure authentication and account management.
- OCR support for scanned study materials.
- Email or push notifications for scheduled reviews.
- Richer progress analytics and teacher dashboards.
- Evaluation of grading quality and learning outcomes with student testing.

## Author

**Imran Hosen**  
GitHub: [@IMRAN-8](https://github.com/IMRAN-8)

## License

This project is distributed under the **MIT License**. See [`LICENSE`](LICENSE) for details.
