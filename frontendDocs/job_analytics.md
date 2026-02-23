# Job Analytics Dashboard (`job-analytics.ejs`)

A data-heavy view providing recruitment funnel and performance insights.

## Purpose
- Visualize candidate conversion rates.
- Identify skill gaps in the applicant pool.
- Analyze score distributions.

## Events & Actions
- **Initial Data Fetch**:
  - The page is rendered with aggregated `analytics` data passed from `jobController.getJobAnalytics`.
- **Responsive Charts**:
  - CSS-based bar charts adapt to the data values (e.g., Conversion Funnel width).

## UI Elements
- **Metric Cards**: Large typography for high-level stats (Views, Shortlisted).
- **Funnel Visualization**: Horizontal bars showing the drop-off from "Apply" to "Complete".
- **Skill Proficiency Bars**: Granular breakdown of average scores per technical skill.
