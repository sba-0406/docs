# Flow Doc 03: Job Search & Discovery

This document explains how the system handles the **Job Search** process, including how it dynamically identifies if a user has already applied to a job.

---

## 🚀 Step 1: Page Load (Frontend)
*   **File**: [jobs-portal.ejs](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/frontend/views/jobs-portal.ejs)
*   **Trigger**: User navigates to `/api/applications/jobs-portal`.

### 🔍 Behind the Scenes (Initial API Call)
When the page loads, the `loadJobs()` function is automatically triggered:
```javascript
async function loadJobs() {
    const response = await fetch('/api/applications/jobs');
    const data = await response.json();
    // ... logic to render jobs
}
```

---

## 🛡️ Step 2: Global Middleware & Identity
*   **File**: [app.js](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/backend/app.js) -> `app.use(loadUser)`

Even though this is a "Public" route, the server runs `loadUser`. 
1.  **Check Cookie**: Looks for the `token` cookie.
2.  **Attach Identity**: If found, it attaches the user profile to `req.user`. This is crucial for the "Applied" check later.

---

## 🧠 Step 3: Database Query & Logic (Controller)
*   **File**: [applicationController.js](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/backend/controllers/applicationController.js) (Method: `getPublicJobs`)

### 🕵️ Execution Trace:
1.  **Query Construction**:
    ```javascript
    const query = { status: 'active' }; // Only show open jobs
    ```
2.  **Filtering**: If the user typed into the search bar, the `req.query` (search, department, location) is added to the MongoDB filter.
3.  **The "Application Check" (The Proactive Logic)**:
    ```javascript
    if (req.user) {
        const userApplications = await Application.find({ candidate: req.user._id });
        const appliedJobIds = new Set(userApplications.map(app => app.job.toString()));
        
        results = jobs.map(job => ({
            ...job,
            isApplied: appliedJobIds.has(job._id.toString())
        }));
    }
    ```
    *   **What's happening?**: The controller compares the list of all jobs against the user's personal application history. It "injects" an `isApplied` boolean into each job object.

---

## 🎨 Step 4: UI Rendering (Frontend)
*   **File**: `jobs-portal.ejs` (Method: `renderJobs`)

The frontend receives the JSON list and loops through it:
```javascript
grid.innerHTML = jobs.map(job => `
    <div class="job-card">
        ${job.isApplied ? '<div class="badge">✓ APPLIED</div>' : ''}
        <h3>${job.title}</h3>
        ...
    </div>
`).join('');
```

### ✅ Final State:
*   **User sees**: A list of jobs. If they are logged in, jobs they already applied for are clearly marked with a badge and have their action changed to "View Status".
*   **Performance**: The search filtering happens in the **Backend** initially, but subsequent typing in the search bar triggers **Frontend** filtering for a faster feel.
