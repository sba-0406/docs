# Job Candidates Table (`job-candidates.ejs`)

A tabular overview of all applicants for a specific job, allowing HR to compare candidates at a glance.

## Purpose
- Sort and filter candidates by name, score, or application date.
- Provide a high-level "Fit" assessment comparison.

## Events & Actions
- **Sorting**:
  - **Trigger**: Clicking table headers (Candidate, Score).
  - **Logic**: Re-fetches the list from the backend with a `?sort=` query parameter.
- **Navigation**: 
  - `View Profile` button leads to `application-detail.ejs`.

## UI Elements
- **Score Badges**: Color-coded (Green/Amber/Red) based on the weighted assessment score.
- **Fit Labels**: Textual indicators (Strong Fit, Moderate Fit, Weak Fit) derived from AI analysis.
- **Status Pills**: Clearly show where each candidate is in the pipeline (e.g., Interview Scheduled).
