# Flow Doc 07: Assessment Generation (AI)

This document explores the "brain" of ScenarioSim: the **AI-Driven Assessment Generator**. It traces how a raw job description is transformed into a structured, technical, and situational examination.

---

## 🚀 Step 1: Trigger (HR Action)
*   **Action**: HR clicks "Generate Assessment" on a Job Detail page.
*   **API Call**: `POST /api/jobs/:id/generate-assessment`

---

## 🧠 Step 2: JD Analysis (The "Deconstruction")
*   **File**: [jdParserService.js](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/backend/services/jdParserService.js) (Method: `analyzeJobDescription`)

### 🕵️ Execution Trace:
1.  **AI Prompting**: The system sends the full JD to **Groq (Llama-3.3-70b)**.
2.  **Structure Discovery**: The AI identifies:
    *   **Technical Skills**: (e.g., React, Python, MongoDB)
    *   **Soft Skills**: (e.g., Leadership, Communication)
    *   **Seniority**: (e.g., Junior vs. Senior)
    *   **Weights**: How much should Code vs. Character matter? (e.g., 70% Technical, 30% Soft).
3.  **JSON Solidification**: The raw text from AI is sanitized, parsed, and validated to ensure weights sum to **1.0**.

---

## 🛠️ Step 3: Question Selection & Generation
*   **File**: [assessmentGeneratorService.js](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/backend/services/assessmentGeneratorService.js)

### 🕵️ Execution Trace:
1.  **Bank Search**: The system first looks at the existing `Questions` collection in MongoDB for matching skills.
2.  **Gap Filling (AI Generation)**: If a skill (like "Mojo" or "Rust") isn't in the bank, it calls [aiService.js](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/backend/services/aiService.js).
3.  **The "Critic Pass" (Extra Detail)**: 
    *   **Brain 1 (The Creator)**: Generates 5 questions for the skill.
    *   **Brain 2 (The Critic)**: A separate AI call reviews those 5 questions for accuracy and clarity.
    *   **Result**: Only validated questions are added to the assessment.

---

## 🎭 Step 4: Scenario Templating (Soft Skills)
*   **File**: `jdParserService.js` (Method: `generateScenarioTemplates`)

Instead of generic questions, the AI creates **Situation Templates** specifically tied to the JD context.
*   **Example**: If the JD mentions "Late night releases", a scenario might be generated about "Managing team morale during a midnight production bug".

---

## 💾 Step 5: Final Assembly & Storage
*   **File**: [jobController.js](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/backend/controllers/jobController.js)

1.  **Assessment Document**: A new document is created in the `Assessments` collection.
2.  **Linking**: The Job document is updated: `job.assessmentId = newAssessment._id`.
3.  **Moderation Status**: If any AI-generated questions were used, the assessment is marked as `pending_review` so HR can double-check them.

### ✅ Final State:
*   **Job Status**: Ready for candidates.
*   **AI Metadata**: The `Assessment` object now contains technical questions, difficulty levels, and situational templates, all calibrated to the specific job description.
