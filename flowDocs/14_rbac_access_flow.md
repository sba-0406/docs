# RBAC & Access Management Flow

This flow details the security architecture of ScenarioSim, ensuring that candidates, HR, and Admins can only access their respective functionalities.

## 1. Role Definitions
- **Model**: `User.js`
- **Roles**:
    - `candidate`: Can browse jobs, apply, and take assessments.
    - `hr`: Can create jobs, moderate questions, and view candidate results/analytics.
    - `admin`: Full access to the system, including system-wide configurations and user management.

## 2. Authentication Middleware
- **Middleware**: `authMiddleware.js` (`protect`)
- **Logic**:
    - Verifies the JWT (JSON Web Token) from the request cookies or headers.
    - Attaches the verified `User` object to `req.user`.

## 3. Authorization Layers
- **Middleware**: `authorizeHR` and `authorizeAdmin`
- **Logic**:
    - Checks `req.user.role` against the required role.
    - Returns a `403 Forbidden` if the user is unauthorized.
- **Application**:
    - `/api/jobs/*` routes are restricted using `protect` and `authorizeHR`.
    - Candidate dashboards are restricted using `protect`.

## 4. Resource Ownership Verification
- **Component**: `applicationController` & `assessmentController`
- **Logic**:
    - Beyond role checks, the system verifies that a candidate only accesses their *own* applications.
    - Example: `Application.findOne({ _id: appId, candidate: req.user._id })`.
    - This prevents horizontal privilege escalation between candidates.

## 5. View-Level Access Control
- **Component**: `_navbar.ejs` & `app.js`
- **Logic**:
    - `res.locals.user` is populated in `app.js` for all EJS templates.
    - The navbar conditionally renders links (e.g., "HR Dashboard") only if `user.role === 'hr'`.
    - This creates a seamless UI experience where unavailable features are hidden from the user.
