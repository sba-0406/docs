# Flow Doc 09: Grading & Results

This document provides a technical deep-dive into the **Scoring Algorithm** that translates a candidate's MCQ answers and simulation choices into a single "Overall Fit" score.

---

## 🚀 Step 1: Trigger (Finalization)
*   **File**: [assessmentController.js](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/backend/controllers/assessmentController.js) (Method: `finalizeAssessment`)
*   **Action**: `POST /api/assessment/finalize`

---

## 🧠 Step 2: Technical Score Calculation (MCQ)
The system retrieves all answers from the `ChatSession`.

### 🕵️ Execution Trace:
1.  **Iterate Answers**: Loops through `session.mcqAnswers`.
2.  **Skill Breakdown**:
    ```javascript
    const techScore = (correctMCQs / totalMCQs) * 100;
    ```
3.  **Sanitization**: Skill names (like "Node.js") are sanitized for database maps (replacing dots with Unicode symbols).

---

## 🎭 Step 3: Soft Skill Score Calculation (Scenario)
The system looks at the final values of the **World State** metrics after all scenario turns are complete.

### 🕵️ Execution Trace:
1.  **Metric Average**:
    ```javascript
    const worldStateEntries = Object.fromEntries(session.worldState);
    const softScore = totalHealth / metricCount;
    ```
    *   *Note*: If the candidate maintained 80% Trust, 70% Morale, and 90% Productivity, their Soft Score would be **80%**.

---

## ⚖️ Step 4: The Weighted Integration
This is where the Job Description's priorities come into play.

### 🕵️ Execution Trace:
1.  **Weight Retrieval**: Gets `technicalWeight` and `softSkillWeight` from the [Assessment](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/backend/models/Assessment.js) model.
2.  **Weighted Total**:
    ```javascript
    const finalScore = (techScore * techWeight) + (softScore * softWeight);
    ```
    *   *Example*: For a Developer role (70% Tech, 30% Soft), a candidate with 100% Tech but 50% Soft would score **85%**.

---

## 🏁 Step 5: Fit Determination & Persistence
*   **Fit labels**: The system assigns a label based on the final percentage:
    *   `>= 85`: **High Potential**
    *   `>= 70`: **Strong Fit**
    *   `>= 50`: **Moderate Fit**
    *   `< 50`: **Low Fit**

### 💾 Database Update:
1.  **Application Model**:
    *   Sets `assessmentStatus: 'completed'`.
    *   Sets `status: 'assessment_completed'`.
    *   Saves the full result object (including skill-by-skill breakdown).
2.  **Session Model**: Marked as `status: 'completed'`.

### ✅ Final State:
*   **Candidate Experience**: They see a summary screen (e.g., "Congratulations! You completed the assessment. Your overall fit is Strong.")
*   **HR View**: The HP Dashboard now shows this candidate's detailed scores, allowing HR to make a data-driven shortlisting decision.
