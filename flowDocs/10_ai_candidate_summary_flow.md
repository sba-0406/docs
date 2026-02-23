# Flow Doc 10: AI Candidate Insights

This document explains the **AI Summarization Flow**, which saves HR managers hours of time by distilling a candidate's entire assessment performance into a concise, professional summary.

---

## 🚀 Step 1: Trigger (HR Action)
*   **File**: [application-detail.ejs](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/frontend/views/application-detail.ejs)
*   **Trigger**: HR navigates to a candidate's profile and clicks the **"Generate AI Summary"** button.
*   **API Call**: `POST /api/applications/application/:id/ai-summary`

---

## 🛡️ Step 2: Request Entry & RBAC
*   **File**: [applicationRoutes.js](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/backend/routes/applicationRoutes.js)

1.  **Identity Check**: `protect` ensures the HR manager is logged in.
2.  **Role Verification**: `authorizeHR` ensures only 'hr' or 'admin' roles can trigger AI generation.

---

## 🧠 Step 3: Logic Execution (Controller)
*   **File**: [applicationController.js](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/backend/controllers/applicationController.js) (Method: `generateAISummary`)

### 🕵️ Execution Trace:
1.  **Prerequisite Check**: 
    ```javascript
    if (application.assessmentStatus !== 'completed') {
        return res.status(400); // Cannot summarize an unfinished test
    }
    ```
2.  **AI Call Initation**:
    *   The controller packages the `candidateName`, `jobTitle`, and the full `assessmentResults` (MCQ and Scenario scores).
    *   It passes this data to the **AI Service**.

---

## 🤖 Step 4: AI Analysis
*   **File**: [aiService.js](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/backend/services/aiService.js) (Method: `generateCandidateSummary`)

### 🕵️ Prompt Engineering Trace:
The system asks the AI (Groq/Llama) to act as a **Professional Recruiter**:
```text
"Generate a professional, 3-4 sentence summary... Focus on technical proficiency, soft skills, and overall suitability... Mention strengths and areas for development."
```
1.  The AI analyzes the numbers (e.g., 90% Tech vs 40% Soft).
2.  It interprets the context (e.g., "This candidate is technically exceptional but struggled with empathy in high-pressure scenarios").
3.  Returns a string of text.

---

## 💾 Step 5: Persistence & UI Feedback
1.  **Storage**: The summary is saved into the `application.reviewNotes` field in MongoDB.
2.  **HR Attribution**: `application.reviewedBy` is set to the current HR manager's ID.
3.  **JSON Return**: The summary is sent back to the frontend.
4.  **UI Update**: The frontend immediately updates the page content without a reload, showing the new summary.

### ✅ Final State:
*   **HR View**: Instead of looking at dozens of MCQ scores and chat transcripts, they see a 4-sentence summary that helps them decide whether to schedule an interview.
*   **Audit**: The database remembers which HR manager generated the summary and on what date.
