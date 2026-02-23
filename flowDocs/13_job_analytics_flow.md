# Job Performance Analytics Flow

This flow explains how ScenarioSim aggregates candidate data into actionable insights for HR teams.

## 1. Data Aggregation
- **Component**: `jobController.getJobAnalytics`
- **Logic**:
    - Retrieves the `Job` by ID to get total `views`.
    - Counts `Application` records for the job, filtered by:
        - Total Applications.
        - Completed Assessments (`assessmentStatus: 'completed'`).
        - Shortlisted Candidates (`status: 'shortlisted'`).

## 2. Metric Calculations
- **Conversion Rates**:
    - **View to Apply**: (Total Apps / Job Views) * 100.
    - **Apply to Complete**: (Completed Assessments / Total Apps) * 100.
    - **Complete to Shortlist**: (Shortlisted / Completed Assessments) * 100.
- **Aggregated Averages**:
    - Calculates the average `technicalScore`, `softSkillScore`, and `weightedScore` across all candidates who completed the assessment.

## 3. Advanced Insights (Dashboard)
- **Component**: `job-analytics.ejs`
- **Visuals**:
    - **Score Distribution**: A bar chart grouping candidates into score buckets (<50, 50-70, 70-85, >85).
    - **Skill Proficiency**: A horizontal bar chart showing the average performance per specific skill (e.g., "Node.js", "Teamwork").
- **Note**: These insights help HR identify if a job description is attracting the right talent or if the assessment is too difficult.

## 4. UI Delivery
- **Logic**:
    - The controller can return either JSON or rendered HTML depending on the `Accept` header.
    - The dashboard uses vanilla CSS and inline data injection for a performant, dependency-free experience.
