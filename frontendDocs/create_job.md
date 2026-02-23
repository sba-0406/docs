# Create Job Page (`create-job.ejs`)

This page allows HR users to initiate the recruitment process by defining a new job and its assessment requirements.

## Purpose
- Capture Job Description (JD) and basic metadata.
- Trigger the AI analysis and assessment generation process.

## Events & Actions
- **JD Upload/Paste**:
  - **Trigger**: Form submission.
  - **Logic**: 
    1. Sends job data to `POST /api/jobs`.
    2. Receives the new Job ID.
    3. Triggers `POST /api/jobs/:id/generate-assessment` which calls `assessmentGeneratorService`.
    4. Redirects the user to the `Job Detail` page to review the AI analysis.

## UI Elements
- **Multi-select Category**: Allows setting the department and location.
- **Interactive Experience Range**: Sliders or number inputs for experience requirements.
