# HR Dashboard (`hr-dashboard.ejs`)

The primary workspace for recruitment teams to manage active job postings.

## Purpose
- Provide a high-level summary of recruitment metrics.
- Organize job postings for quick moderation and review.

## Events & Actions
- **Initial Load**:
  - **Function**: `loadJobs()`.
  - **Logic**: Fetches `GET /api/jobs` and populates the `allJobs` cache.
- **Stats Calculation**:
  - **Function**: `updateStats()`.
  - **Logic**: Aggregates data from the job list to show total jobs, active jobs, and total applications.
- **Filtering**:
  - **Trigger**: Clicking filter tabs (All, Active).
  - **Logic**: Re-filters the local `allJobs` array and re-renders the list without a network request.
- **Job Card Actions**:
  - `Details`: Link to management page.
  - `Candidates`: Link to the applicant list.
  - `Analytics`: Link to performance charts.

## UI Elements
- **Stat Overview Cards**: Real-time summary of the hiring pipeline.
- **Job Status Badges**: clearly demarcates `draft`, `active`, and `closed` postings.
- **Responsive Grid**: Adapts the job card layout for different screen sizes.
