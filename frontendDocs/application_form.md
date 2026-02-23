# Application Submission Form (`application-form.ejs`)

The gateway for candidates to enter the recruitment pipeline.

## Purpose
- Collect candidate contact info and experience history.
- Accept resume file uploads.

## Events & Actions
- **File Validation**:
  - **Trigger**: Selecting a file.
  - **Logic**: Ensures only PDF or Word documents under 5MB are accepted.
- **Form Submission**:
  - **Trigger**: `submit` event.
  - **Logic**: 
    1. Uses `FormData` to package text inputs and the file.
    2. Sends a `POST` request to `/api/applications`.
    3. On success: Redirects to the `Candidate Dashboard`.

## UI Elements
- **Upload Dropzone**: A styled container for file selection with immediate name feedback.
- **Success/Error Toast**: Provides instant feedback on the upload status.
