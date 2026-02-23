# Shared Client Logic (`main.js`)

The core utility file included in all frontend views to manage session persistence and shared interactions.

## Purpose
- Handle global authentication states.
- Manage consistent navigation experiences.

## Key Events & Actions
- **`DOMContentLoaded`**:
  - **Logic**: Checks `localStorage` for a `user` object.
  - **Action**: Dynamically updates the navbar links based on the user's role (Candidate vs HR).
- **Logout Event**:
  - **Trigger**: Clicking any element with `id="logoutBtn"`.
  - **Logic**: 
    1. Sends `/api/auth/logout`.
    2. Wipes `localStorage`.
    3. Redirects to `/login`.
- **RBAC Redirection**:
  - If a guest tries to access protected paths like `/dojo/roles`, the logic forces a redirect to the login page.

## Note on Navigation
- While `_navbar.ejs` (server-side) handles most navigation, `main.js` provides a client-side fallback and dynamic identity display (the user's name in the corner).
