# Flow Doc 04: Job Management (HR Dashboard)

This document provides a microscopic trace of how HR professionals manage the recruitment lifecycle—from accessing their private dashboard to creating new job postings.

---

## 🚀 Step 1: Accessing the Dashboard
*   **Trigger**: HR user logs in and is redirected to `/api/jobs/dashboard`.
*   **File**: [jobController.js](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/backend/controllers/jobController.js) (Method: `renderHRDashboard`)

### 🔍 Behind the Scenes (RBAC Check)
The `jobRoutes.js` file protects this route with `authorizeHR`:
```javascript
router.get('/dashboard', protect, authorizeHR, jobController.renderHRDashboard);
```
1.  **Identity Verification**: `protect` middleware ensures a valid JWT is in the cookie.
2.  **Role Verification**: `authorizeHR` checks if `req.user.role` is 'hr' or 'admin'. If a 'candidate' attempts this, they receive a **403 Forbidden** error.

---

## 🛡️ Step 2: Request Entry (Initial API Call)
When the HR Dashboard renders, it immediately requests the list of active jobs managed by that specific HR user.

*   **URL**: `GET /api/jobs`
*   **Initial `req` Object**: 
    ```json
    {
      "user": { "id": "hr_123", "role": "hr", "name": "HR Manager" },
      "query": { "status": "active" }
    }
    ```

---

## 🧠 Step 3: Logic Execution (Fetch Jobs)
*   **File**: `jobController.js` (Method: `getJobs`)

### 🕵️ Execution Trace:
1.  **Ownership Filter**:
    ```javascript
    if (req.user.role === 'hr') {
        query.postedBy = req.user._id; // HR only sees THEIR jobs
    }
    ```
2.  **Population**: The query includes `.populate('assessmentId')`, which tells Mongoose to go find the linked Assessment document and attach it to the Job object.
3.  **JSON Return**: Returns a list of jobs to the frontend dashboard.

---

## 📝 Step 4: Creating a New Job
*   **Trigger**: HR clicks "Create Job" button.
*   **Action**: `POST /api/jobs`

### 🕵️ Data Journey:
1.  **Frontend Form**: HR fills out title, description, and "Require Assessment" toggle in `create-job.ejs`.
2.  **Transmission**: Data is sent as a JSON object in the `req.body`.
3.  **Controller Trace (`createJob`)**:
    *   **Extraction**: Destructures all fields from `req.body`.
    *   **Injection**: Automatically attaches `postedBy: req.user._id` (the current HR's ID).
    *   **Defaulting**: Sets `status: 'active'` so it appears in the portal immediately.
4.  **Database Storage**: A new record is inserted into the `Jobs` collection.

---

## 🏁 Step 5: Post-Creation Logic (Summary)
After a job is created, the system doesn't stop there. 
1.  **Job ID returned**: The backend sends back the new Job object (including its MongoDB `_id`).
2.  **Frontend Logic**: The app usually redirects the user to the "Candidate & Assessment" management page for that specific job.

### ✅ Final State:
*   **Portal**: Candidates can now see the new job.
*   **HR View**: The new job appears in the "Active" column of the HR dashboard.
