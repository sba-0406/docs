# Flow Doc 05: Application Submission (Resume Upload)

This document traces the complex process of submitting a job application, specifically focusing on how **Binary Files (Resumes)** are handled, validated, and stored.

---

## 🚀 Step 1: User Action (Frontend UI)
*   **File**: [application-form.ejs](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/frontend/views/application-form.ejs)
*   **Trigger**: User fills the form, selects a file, and clicks "Submit Application".

### 🔍 Behind the Scenes (Multi-Part Data)
Since we are sending a file, we cannot use a simple JSON object. We use `FormData`:
```javascript
const formData = new FormData(e.target); // Captures text + file
const response = await fetch(`/api/applications/apply/${jobId}`, {
    method: 'POST',
    body: formData // No Content-Type header needed; browser sets it automatically
});
```

---

## 🛡️ Step 2: The Gatekeeper (Multer Middleware)
*   **File**: [applicationRoutes.js](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/backend/routes/applicationRoutes.js)

Before the request ever reaches our controller, it hits **Multer**:
1.  **Size Check**: Rejects files larger than 5MB.
2.  **Format Check**: Only allows `.pdf`, `.doc`, or `.docx` (via regex filter).
3.  **Storage Engine**: 
    *   **Destination**: Saves the file to `backend/uploads/resumes/`.
    *   **Naming**: Renames the file to `resume-timestamp-random.ext` to prevent overwriting files with the same name (e.g., `resume.pdf`).

### 📦 Updated `req` Object
Multer modifies the `req` object:
*   `req.body`: Now contains the text fields (name, email, etc.).
*   `req.file`: contains the metadata of the uploaded file (path, filename, size).

---

## 🧠 Step 3: Logic Execution (Controller)
*   **File**: [applicationController.js](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/backend/controllers/applicationController.js) (Method: `submitApplication`)

### 🕵️ Execution Trace:
1.  **Duplicate Check**: 
    ```javascript
    const existing = await Application.findOne({ job: jobId, candidate: req.user._id });
    if (existing) return res.status(400); // Prevent double applying
    ```
2.  **Data Assembly**: 
    *   Extracts info from `req.body`.
    *   Finds the resume path from `req.file.path`.
3.  **Database record created**: An `Application` document is saved in MongoDB.
4.  **Job Counter**: The `applications` count on the `Job` model is incremented by 1.

---

## 🏁 Step 4: Outcome & Redirection
*   **Response**: The server sends back the newly created Application ID.
*   **Frontend**: The script redirects the candidate to their specific **Application View** page.

### ✅ Final State:
*   **FileSystem**: The actual `.pdf` or `.doc` file is now sitting in the `uploads/resumes/` folder.
*   **Database**: A record exists that "points" to that file location.
*   **User Experience**: The candidate receives instant feedback that their application was successful.
