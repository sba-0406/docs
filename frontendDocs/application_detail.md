# Application Detail View (`application-detail.ejs`)

A dual-purpose view that serves both candidates (tracking their progress) and HR (reviewing candidates).

## Purpose
- **For Candidates**: Shows a step-by-step progress tracker and assessment results.
- **For HR**: Provides "Recruiter Actions" and AI-generated performance insights.

## Events & Actions
- **Recruiter Actions (HR/Admin only)**:
  - **Shortlist/Reject**: `updateStatus(newStatus)` calls `PATCH /api/applications/application/:id/status`.
  - **Schedule Interview**: `scheduleInterview()` prompts for a date and updates the application status logic.
- **AI Integration**:
  - **Generate Summary**: `generateAISummary()` calls `POST /api/applications/application/:id/ai-summary`. This fetches a breakdown of the candidate's strengths and weaknesses based on their simulation performance.
- **Assessment Management**:
  - **Start/Resume**: Provides a direct link to `dojo-assessment.ejs` if the assessment is not yet finished.

## UI Elements
- **Vertical Progress Steps**: A visual timeline from "Application Submitted" to "Final Decision".
- **Scorecard Segment**: Displays Technical vs Soft Skill percentages and the "Overall Fit" rating.
- **Resume Portal**: A quick-access button to view the uploaded document.
