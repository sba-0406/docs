# Navbar Partial (`_navbar.ejs`)

The navbar is the primary navigation component, dynamically adapting its layout based on the user's role and authentication status.

## Purpose
- Provide global navigation links.
- Enforce Role-Based Access Control (RBAC) at the UI level.
- Display user identity and logout options.

## Key Logic (RBAC)
The navbar uses EJS conditionals to check the `user` object passed from the backend:
- **Authenticated (HR/Admin)**:
  - Links: `HR Dashboard`, `Post a Job`.
- **Authenticated (Candidate)**:
  - Links: `Browse Jobs`, `My Applications`.
- **Unauthenticated**:
  - Links: `Explore Jobs`, `Login`, `Sign Up`.

## Events & Actions
- **Logout Action**: 
  - **Trigger**: Clicking the "Logout" link.
  - **Function**: `logout(e)`
  - **Logic**: Calls `GET /api/auth/logout`, removes the `user` from `localStorage`, and redirects to `/login`.

## CSS Integration
- Uses `.active` class to highlight the current page based on the `currentPath` variable.
- Uses `.nav-icon` for visual branding of links.
