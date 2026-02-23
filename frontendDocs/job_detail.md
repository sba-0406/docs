# Job Detail Page (`job-detail.ejs`)

The management cockpit for a specific job posting, focusing on assessment quality and configuration.

## Purpose
- Review AI-extracted skills and role classification.
- Moderate AI-generated questions.
- Fine-tune assessment weights and passing thresholds.

## Events & Actions
- **Question Moderation**:
  - **Trigger**: Clicking `Approve` or `Reject` on a question.
  - **Action**: `moderateQuestion(id, action)` calls `PUT /api/jobs/:id/questions/:questionId`.
- **Weight Adjustment**:
  - **Trigger**: Moving sliders for Technical vs Soft Skills.
  - **Logic**: Automatically ensures the total equals 100%.
- **Saving Configuration**:
  - **Action**: `saveAssessmentConfig()` calls `PUT /api/jobs/:id/assessment`.
- **Regeneration**:
  - **Action**: `regenerateQuestions()` calls `POST /api/jobs/:id/regenerate-questions`.
- **Publishing**:
  - **Action**: `publishJob()` updates job status to `active`, making it visible to candidates.

## UI Elements
- **Warning Boxes**: Dynamic alerts for "Moderation Required" or "Skill Gaps Detected".
- **Question Preview Cards**: Color-coded by difficulty with source tags (AI GEN).
