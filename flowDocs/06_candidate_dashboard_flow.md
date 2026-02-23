# Flow Doc 06: Candidate Dashboard

This document traces how a candidate views their application history and progresses to the next stage (taking an assessment).

---

## 🚀 Step 1: Request Entry (Frontend)
*   **File**: [candidate-dashboard.ejs](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/frontend/views/candidate-dashboard.ejs)
*   **Trigger**: Page load calls `loadApplications()`.

### 🔍 Behind the Scenes (Identity Check)
*   **URL**: `GET /api/applications/my-applications`
*   **Identity**: The `protect` middleware ensures the request has a valid `token`.

---

## 🧠 Step 2: Logic Execution (Controller)
*   **File**: [applicationController.js](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/backend/controllers/applicationController.js) (Method: `getMyApplications`)

### 🕵️ Execution Trace:
1.  **Ownership Query**:
    ```javascript
    const applications = await Application.find({ candidate: req.user._id })
        .populate('job', 'title department location companyName')
        .sort({ appliedAt: -1 });
    ```
    *   **What's happening?**: The database only returns applications belonging to the logged-in candidate.
    *   **Data Enrichment**: `.populate('job')` brings in the job title and company details so the UI isn't just showing random IDs.
2.  **JSON Return**: The list is sent back to the frontend.

---

## 🎨 Step 3: UI Rendering (The "Action" Logic)
*   **File**: `candidate-dashboard.ejs` (Method: `renderApplications`)

The frontend calculates whether to show "Take Assessment", "Resume Assessment", or "✓ Completed" based on the `assessmentStatus` field:
```javascript
if (app.assessmentStatus === 'pending') {
    assessmentAction = `<a href="/dojo/assessment/${app._id}">Take Assessment</a>`;
} else if (app.assessmentStatus === 'in_progress') {
    assessmentAction = `<a href="/dojo/assessment/${app._id}">Resume Assessment</a>`;
}
```

---

## 🔐 Step 4: Redirection to Assessment
*   **Action**: Clicking "Take Assessment".
*   **Destination**: `/dojo/assessment/${app._id}`.
*   **The Transition**: This moves the user from the "Management" phase (Applications) into the "Active Simulation" phase (Assessments).

### ✅ Final State:
*   **User View**: List of all jobs applied for, their current recruitment status (Rejected, Shortlisted, etc.), and their assessment progress.
*   **Security**: Candidates can **only** see their own applications. If they try to guess another user's ID, the controller filter `candidate: req.user._id` will return an empty list or block access.
