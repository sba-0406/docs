# Flow Doc 08: Assessment Execution (The Simulation)

This document traces the **Active Assessment Journey**, explaining how the system transitions between technical multiple-choice questions (MCQ) and situational AI-driven scenarios.

---

## 🚀 Step 1: Initialization (The Handshake)
*   **File**: [assessmentController.js](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/backend/controllers/assessmentController.js) (Method: `startAssessment`)
*   **Trigger**: Candidate clicks "Take Assessment" on their dashboard.

### 🔍 Behind the Scenes:
1.  **Identity Verification**: Checks if the candidate truly owns this application.
2.  **ChatSession Creation**: If no session exists, the server creates a `ChatSession` document.
    *   **Phase Set**: `assessmentPhase` is set to `MCQ`.
    *   **Progress Tracking**: `currentMCQIndex` starts at 0.
    *   **Scenario Loading**: Templates from the assessment are loaded into `scenarioProgress`.

---

## ⚙️ Step 2: Phase 1 — Technical MCQ
The frontend loops through questions by calling `GET /api/assessment/session/:id`.

### 🕵️ Execution Trace:
1.  **Fetch**: Server looks at `session.currentMCQIndex` and returns exactly **one** question.
2.  **Submit**: Candidate selects an answer/index.
    *   **Endpoint**: `POST /api/assessment/submit-mcq`
    *   **req.body**: `{ sessionId, answerIndex }`
3.  **Grading**: 
    ```javascript
    const isCorrect = question.correctAnswer === parseInt(answerIndex);
    session.mcqAnswers.push({ questionId, answerIndex, isCorrect, skill });
    ```
4.  **Transition**: Once all questions are answered, the controller automatically updates `session.assessmentPhase = 'SCENARIO'`.

---

## 🎭 Step 3: Phase 2 — Situational Simulation
The UI changes from Question/Options to a **Chat Interface**.

### 🕵️ Execution Trace:
1.  **Initial Load**: Candidate sees a stakeholder (e.g., "Product Manager") presenting a problem.
2.  **Response**: Candidate selects a dialogue option or types a response.
    *   **Endpoint**: `POST /api/assessment/respond`
3.  **AI Response Logic**:
    *   The **Groq AI** processes the candidate's last message + the scenario description.
    *   It generates a "human-like" rebuttal or agreement.
4.  **World State Impact**: Depending on the *approach* (Results vs. Relationship), the **World State** metrics (`Trust`, `TeamMorale`, `Productivity`) are updated.
    *   *Example*: Choosing a "Results-only" path might increase Productivity but decrease Team Morale.

---

## 🏁 Step 4: Finalization
*   **Trigger**: Candidate completes 3 scenarios (assessment limit).
*   **Endpoint**: `POST /api/assessment/finalize`

### ✅ Final State:
*   **ChatSession**: Marked as `status: 'completed'`.
*   **User Experience**: The candidate sees a "Submitting..." screen before being redirected to their results.
*   **Database**: All answers, chat transcripts, and world-state scores are locked in preparation for grading (documented in Flow Doc 09).
