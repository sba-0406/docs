# Frontend Documentation Overview

This directory contains detailed explanations of the ScenarioSim frontend architecture, including view files, client-side logic, and event handling.

## Architecture Pattern
- **Engine**: EJS (Embedded JavaScript) for server-side rendering.
- **Styling**: Vanilla CSS (`style.css`) with CSS Variables for theme management.
- **Interactivity**: Vanilla JavaScript (no heavy frameworks) for a lightweight, beginner-friendly experience.
- **State Management**: Minimal use of `localStorage` for user persistence; primary state is managed via backend sessions and cookies.

## View Mapping

### Authentication
- [login.ejs](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/documentation/frontendDocs/login.md) - User sign-in interface.
- [register.ejs](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/documentation/frontendDocs/register.md) - Account creation interface.

### Candidate Experience
- [jobs-portal.ejs](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/documentation/frontendDocs/jobs_portal.md) - Public job board.
- [job-detail-public.ejs](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/documentation/frontendDocs/job_detail_public.md) - Expanded job info for non-applicants.
- [application-form.ejs](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/documentation/frontendDocs/application_form.md) - Resume upload and application submission.
- [candidate-dashboard.ejs](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/documentation/frontendDocs/candidate_dashboard.md) - Tracking applications and starting assessments.
- [dojo-assessment.ejs](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/documentation/frontendDocs/dojo_assessment.md) - The core AI simulation interface for candidates.

### HR Experience
- [hr-dashboard.ejs](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/documentation/frontendDocs/hr_dashboard.md) - Overview of job postings and pipeline stats.
- [create-job.ejs](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/documentation/frontendDocs/create_job.md) - Job posting creation and JD upload.
- [job-detail.ejs](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/documentation/frontendDocs/job_detail.md) - Managing assessments and question moderation.
- [job-candidates.ejs](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/documentation/frontendDocs/job_candidates.md) - Reviewing and sorting applicants.
- [job-analytics.ejs](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/documentation/frontendDocs/job_analytics.md) - Conversion funnels and skill proficiency charts.

### Shared Components
- [_navbar.ejs](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/documentation/frontendDocs/navbar.md) - Global navigation with RBAC logic.
- [main.js](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/documentation/frontendDocs/main_js.md) - Client-side utilities and logout handling.
