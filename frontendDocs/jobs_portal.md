# Public Jobs Portal (`jobs-portal.ejs` & `job-detail-public.ejs`)

The candidate-facing landing pages for job discovery.

## Purpose
- **Portal**: List all active job postings.
- **Public Detail**: Provide the full Job Description for non-logged-in or prospective applicants.

## Events & Actions (Portal)
- **Search & Filter**:
  - **Trigger**: Typing in the search bar or changing filters.
  - **Logic**: Filters the list of jobs displayed on the client-side.
- **Apply Action**: 
  - Clicking `Apply Now` redirects to the `Application Form`.

## Events & Actions (Public Detail)
- **Role Info Display**: Renders JSON-LD and meta tags for SEO.
- **Call to Action**: Dynamic button changes if the user is already logged in or has already applied.

## UI Elements
- **Job Cards**: Consistent branding with location, department, and experience markers.
- **Empty State**: Displays if no jobs match the current search criteria.
