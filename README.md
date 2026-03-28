ApplyWiz — AI Resume Tailoring Agent
Option 2 of the ApplyWiz Engineering Take-Home Challenge
Demo Video
https://drive.google.com/file/d/1jxZ9fU-WCYApyWq3wNpqL9TgNI6UM15P/view?usp=sharing

https://drive.google.com/file/d/1jxZ9fU-WCYApyWq3wNpqL9TgNI6UM15P/view?usp=sharing


Why I Chose Option 2
Option 2 directly showcases AI-assisted pipeline design and prompt engineering — skills that are core to what ApplyWiz is building. The quality of resume tailoring is visible and measurable, making it a stronger demonstration than a screenshot tool.
________________________________________
How It Works
option2_job_links.xlsx  ──┐
                           ├──> Join on id/# ──> 5 jobs
option2_jobs.json       ──┘

resume/candidate_resume.docx ──> base resume text

For each job:
  1. Build a tailoring prompt (job description + resume)
  2. Call Groq AI (Llama 3.3-70b) → get tailored resume text
  3. Save as Word document (.docx)
  4. Send email with resume attached + job URL in body

Each job runs independently — one failure never stops the rest.
Retries up to 3 times with exponential backoff on AI call failures.
________________________________________
Project Structure
applywiz-challenge/
├── agent.py                   # Main pipeline — all logic here
├── option2_job_links.xlsx     # Job metadata (title, company, URL)
├── option2_jobs.json          # Full job descriptions + requirements
├── resume/
│   └── candidate_resume.docx # Input resume (not committed)
├── tailored_resumes/          # Output Word docs (auto-created, not committed)
├── requirements.txt
├── .env.example
├── .gitignore
└── README.md
________________________________________
Setup & Installation
Prerequisites
•	Python 3.9+
•	A Groq account with API key — free at console.groq.com
•	A Gmail account with an App Password enabled
1. Clone the repository
git clone https://github.com/YOUR_USERNAME/applywiz-challenge.git
cd applywiz-challenge
2. Install dependencies
pip install -r requirements.txt
3. Add your resume
Place your resume file at:
resume/candidate_resume.docx
4. Configure environment variables
cp .env.example .env
Edit .env and fill in your credentials:
GROQ_API_KEY=gsk_your_key_here
GMAIL_USER=you@gmail.com
GMAIL_APP_PASSWORD=your_16_char_app_password
RECIPIENT_EMAIL=you@gmail.com
Getting a Gmail App Password:
1.	Go to myaccount.google.com → Security
2.	Enable 2-Step Verification
3.	Search "App Passwords" → generate one for Mail
5. Run the agent
python agent.py
________________________________________
Key Design Decisions
LLM Choice — Groq (Llama 3.3-70b)
Used Groq's free API with the llama-3.3-70b-versatile model. Groq offers extremely fast inference with a generous free tier — no credit card required. The 70b model produces high-quality, nuanced resume tailoring.
Prompt Engineering
The prompt instructs the model to:
•	Keep all factual information intact (no hallucinated experience)
•	Reorder bullets so the most relevant ones appear first
•	Reword to mirror the job's exact language and keywords
•	Rewrite the professional summary for each specific role
•	Add a KEY SKILLS section prioritising role-relevant skills
This produces noticeably different resumes for Backend, Frontend, ML, DevOps, and Full Stack roles even from the same base resume.
Error Handling & Retry Logic
•	Each job runs in an independent try/except block
•	AI calls retry up to 3 times with exponential backoff (2s, 4s, 8s)
•	Failures are logged clearly; the pipeline continues to the next job
•	A summary at the end shows exactly which jobs succeeded or failed
Document Generation
Used python-docx to generate Word documents. Chosen over PDF because:
•	No font/encoding issues (PDF libraries struggle with AI-generated special characters)
•	Word documents are editable — useful for candidates who want to tweak further
•	python-docx has zero system dependencies, works cross-platform
Email Delivery
Used Python's built-in smtplib over SSL with Gmail. No third-party service needed — just a Gmail App Password.
________________________________________
Assumptions
•	The candidate resume has enough breadth to be meaningfully tailored for all 5 roles
•	option2_jobs.json id field maps 1:1 with the # column in the Excel file
•	All 5 emails go to the same recipient (could easily be per-job recipients)
•	Groq free tier is sufficient for 5 LLM calls (it is — very generous limits)
________________________________________
What I Would Improve Given More Time
1.	Async parallel processing — run all 5 LLM calls concurrently with asyncio to reduce total runtime from ~60s to ~15s
2.	PDF output — use reportlab for better PDF formatting alongside the .docx
3.	Web scraping — fetch live job descriptions from real URLs instead of using the provided JSON
4.	Resume scoring — add a second LLM call to score how well each tailored resume matches the job (0-100%) and include that in the email
5.	Structured logging — replace print statements with Python's logging module for production-grade observability
________________________________________
Environment Variables
Variable	Description
GROQ_API_KEY	Groq API key from console.groq.com
GMAIL_USER	Gmail address used to send emails
GMAIL_APP_PASSWORD	16-character Gmail App Password
RECIPIENT_EMAIL	Email address to receive tailored resumes

