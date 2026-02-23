# Login Page (`login.ejs`)

The login page serves as the entry point for existing users to access their accounts.

## Purpose
- Authenticate users via email and password.
- Persist user data in the browser for UI personalization.

## Events & Actions
- **Login Submission**:
  - **Trigger**: `loginForm.onsubmit`
  - **Function**: Inline asynchronous function.
  - **Logic**:
    1. Collects `email` and `password` from inputs.
    2. Sends a `POST` request to `/api/auth/login`.
    3. On success:
       - Stores the full user object (name, role, etc.) in `localStorage`.
       - Redirects based on role:
         - **HR/Admin**: Redirects to `/api/jobs/dashboard`.
         - **Candidate**: Redirects to `/api/applications/jobs-portal`.
    4. On error: Displays an alert and clears the password field.

## UI Elements
- **Interactive Feedback**: Changes button text to "Signing in..." during the request.
- **Form Validation**: Native HTML5 validation for email formatting and required fields.
