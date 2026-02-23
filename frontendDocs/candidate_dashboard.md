# Candidate Dashboard (`candidate-dashboard.ejs`)

The dashboard is the central hub for candidates to track their job applications and start assessments.

## Purpose
- Display a list of all jobs the candidate has applied for.
- Provide direct links to start/resume assessments or view details.

## Events & Actions
- **Initial Load**:
  - **Trigger**: `DOMContentLoaded`.
  - **Function**: `loadApplications()`.
  - **Logic**: Fetches `GET /api/applications/my-applications` and passes data to the renderer.
- **Dynamic Rendering**:
  - **Function**: `renderApplications(applications)`.
  - **Logic**:
    - Generates "Application Cards" for each result.
    - **Conditional Buttons**:
      - `Take Assessment`: Shown if `assessmentStatus` is `pending`.
      - `Resume Assessment`: Shown if `assessmentStatus` is `in_progress`.
      - `Completed`: A non-interactive status badge shown once finalized.
- **Navigation**:
  - `Details` button links to the specific application detail view.

## UI Elements
- **Status Badges**: Color-coded (Success, Warning, Danger) based on whether the application is applied, shortlisted, or rejected.
- **Empty State**: Displays a friendly message if the candidate hasn't applied to any jobs yet.
